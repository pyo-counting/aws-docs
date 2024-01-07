사용자가 route 53을 통해 도메인을 구입하면 DNS 서비스 제공을 위해 아래 작업을 자동으로 수행한다.
- 도메인 이름과 같은 public hosted zone을 생성
- hosted zone에 4개의 도메인의 Authoritative name server를 NS 레코드에 등록한다.
- registrar에 사용자 입력 정보를 전송한다.