EC2 인스턴스에서 실행 중인 애플리케이션이 DNS 쿼리 수행할 때 route 53 vpc resolver는 아래 동작을 수행한다.
1. rule 목록에서 매칭되는 domain을 찾는다. 만약 매칭되는 rule이 있는 경우 vpc resolver는 outbound endpoint로 쿼리한다. 그 다음 outbound endpoint는 해당 domain과 매칭되는 rule의 target ip로 쿼리를 다시 전달한다.
2. 어떤 rule에도 매칭되지 않는 경우 vpc resolver는 autodefined "." (dot) rule에 따라 쿼리를 인터넷으로 forwarding한다. 해당 rule은 AWS 내부 domain, private hosted zone에 정의된 reocrd에는 예외적으로 적용되지 않는다. 이 rule을 통해 resolver는 쿼리를 public name server로 forwarding한다.
3. resolver는 쿼리에 대해 응답한다.