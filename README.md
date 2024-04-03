# aws-partner-summit-docs
AWS Partner Summit 2024 핸즈온 실습 가이드 문서

## 사전 요구사항

### AWS Workshop Studio


### TFE 로그인 및 패스워드 초기화

- `hashicorp.secureaws.net` 접속 후 화면

<img src="https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/CNPU60.jpg" width="50%" >
<!-- ![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/CNPU60.jpg) -->


- "Log in via SAML" 클릭 후 Keycloak 화면으로 전환

> 배정된 좌석의 사용자 명으로 로그인.

<img src="https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/VoKvuj.jpg" width="50%" >

- 초기 계정 및 패스워드 입력 후 패스워드 초기화

<img src="https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/A9x94l.jpg" width="50%" >

- 접속 후 할당된 Org의 Invitations을 수락(Accpet)

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/oGHWdc.jpg)

- 실습을 위한 6개의 Workspace 확인

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/dzrKO6.jpg)

### 실습을 위한 AWS 크리덴셜 설정

- 좌측의 [Settings] 클릭
![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/GaD9wc.jpg)

- [Organization Settings] - [Variable sets] 탭에서 "PoC Varset" 클릭

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/J4o2Do.jpg)

- AWS Workshop Studio을 통해 발급된 `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`에 대한 값 추가

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/eHxi2E.jpg)

- 입력 후 예시화면 : `AWS_SECRET_ACCESS_KEY` 값은 Sensitive 처리

![img](https://raw.githubusercontent.com/hyungwook0221/img/main/uPic/K5Peyi.jpg)