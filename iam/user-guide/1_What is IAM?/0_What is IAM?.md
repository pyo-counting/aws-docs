aws iam(identity and access management)는 aws resource에 대한 접근을 안전하게 제어할 수 있는 웹 서비스다. iam을 사용해 resource를 사용할 수 있는 사용자의 인증(authentication)과 인가(authorization)를 제어할 수 있다.

aws 계정을 생성하면 해당 계정의 모든 aws service, resource에 대한 완전한 접근 권한이 있는 하나의 로그인 ientity를 갖는다. 이 identity를 aws account `root user`라고 부른다. 해당 identity는 필요할 떄만 사용하고 이외에는 사용하지 않는 것을 권장한다.