# 03. Sentinel 시나리오
Sentinel을 적용한 Policy as Code을 이해하고 Shift Left 보안 고도화 사례 학습

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