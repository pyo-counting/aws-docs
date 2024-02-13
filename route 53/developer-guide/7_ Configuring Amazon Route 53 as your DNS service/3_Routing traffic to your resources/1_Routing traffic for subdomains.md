subdomain에 대한 라우팅 설정 방법은 두 가지가 있다.
- hosted zone 내에서 subdomain에 대한 record 생성: 일반적인 방법으로 hosted zone 내에서 subdomain에 대한 record를 생성한다.
- subdomain에 대한 새로운 hosted zone 생성 후 record 생성: subdomain에 대한 라우팅을 위해 새로운 hosted zone을 생성하는 것을 "delegating responsibility for a subdomain to a hosted zone" 또는 "delegating a subdomain to other name servers"라고 부른다. 분리된 hosted zone을 사용해 IAM 권한을 통해 접근을 제어할 수 있다.