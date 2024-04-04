# 02. Sentinel 시나리오
Sentinel을 적용한 Policy as Code을 이해하고 Shift Left 보안 고도화 사례 학습

### 1-1)Workspace : Security Group 배포

- Default VPC에 Security Group을 배포하는 시나리오
- Security Group의 Ingress IP CIDR 대역을 관리할 수 있도록 `variable.tf`에 설정되어 있음
- 아래 코드를 참고하여 Ingress IP CIDR에 `0.0.0.0/0` 대역으로 보안그룹 생성

#### 샘플코드

- main.tf
```ruby
resource "aws_security_group" "example" {
  name   = "sentinel-test-sg"
  description = "Example security group for EC2 instances"

  ingress {
    from_port   = 8081
    to_port     = 8081
    protocol    = "tcp"
    
    ## 보안 취약점: 인터넷에 대한 액세스를 제어하지 않음
    # cidr_blocks = ["0.0.0.0/0"]
    # cidr_blocks = ["192.168.0.100/32"]
    cidr_blocks = [var.cidr_blocks]
    description = "Allow all inbound traffic"
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    
    # cidr_blocks = ["0.0.0.0/0"]
    cidr_blocks = [var.cidr_blocks]
    description = "Allow all outbound traffic"
  }

  tags = {
    Name = "sentinel-test-sg"
  }
}

```
- variables.tf
```ruby
variable "cidr_blocks" {
  type = string
  default = "0.0.0.0/0"
}
```

#### (1) 비정상적인 상황 🚨
- Security Group 리소스 배포. 이때 Ingress IP CIDR은 `0.0.0.0/0`
  - Variables 값이 cidr_blocks = `0.0.0.0/0` 으로 설정되어 있음

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/g9v2mq.jpg)

<!-- ![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/hBJ5Rf.jpg) -->

- Terraform Plan 이후 Sentinel에서 Policies Failed 발생

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/xZmtnD.jpg)

- 이유는 `0.0.0.0/0`은 배포되지 못하도록 Hard Mandatory 설정이 되어 있기 때문

#### (2) 정상적인 상황 ✅
- Security Group의 Ingress IP CIDR 대역을 "사용자 IP 주소(또는 특정 IP 
대역)"로 변경
  - Variables 값을 cidr_blocks = `192.168.0.100/32` 으로 변경

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/fTwmG3.jpg)

- Security Group 리소스 재배포 후 Sentinel에서 문제 없음을 확인

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/swIBqP.jpg)

- 리소스 생성확인

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/GrGEIO.jpg)


### 1-2) Workspace : S3 배포
- ACL 활성화된 S3배포하는 시나리오
- variable.tf에 ACL private 설정 여부를 관리할 수 있도록 default 변수 설정 
- 아래 코드를 참고하여 ACL 비활성화된 S3 배포

#### 샘플코드

- main.tf
```ruby
resource "random_id" "example" {
  byte_length = 8
}

resource "aws_s3_bucket" "example" {
  bucket = "s3-bucket-sentinel-${random_id.example.hex}"
}

resource "aws_s3_bucket_ownership_controls" "example" {
  bucket = aws_s3_bucket.example.id
  rule {
    object_ownership = "BucketOwnerPreferred"
  }
}

resource "aws_s3_bucket_acl" "example" {
  depends_on = [aws_s3_bucket_ownership_controls.example]

  bucket = aws_s3_bucket.example.id
  acl    = var.acl_enabled ? "private" : "public-read"
}

resource "aws_s3_bucket_public_access_block" "example" {
  bucket = aws_s3_bucket.example.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}
```

- variables.tf
```ruby
variable "acl_enabled" {
  type    = bool
  default = false # ACL을 활성화하려면 true로 설정하세요
}
```

#### (1) 비정상적인 상황 🚨
- S3 버킷 배포. 이때 ACL 은 `enabled=false`이므로 "Public-read" 설정되어 있음
- Terraform Plan 이후 Sentinel에서 Policies Failed 발생

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/wOoNfW.jpg)

- 이유는 ACL Private 설정을 강제화 하는 Hard Mandatory 설정이 되어있기 

#### (2) 정상적인 상황 ✅
- S3 버킷 ACL 설정을 `enabled=true`로 변경

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/2PZW1r.jpg)

- Security Group 리소스 재배포 후 Sentinel에서 문제 없음을 확인

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/kcyvqn.jpg)

- 리소스 생성 확인

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/74Jlti.jpg)
