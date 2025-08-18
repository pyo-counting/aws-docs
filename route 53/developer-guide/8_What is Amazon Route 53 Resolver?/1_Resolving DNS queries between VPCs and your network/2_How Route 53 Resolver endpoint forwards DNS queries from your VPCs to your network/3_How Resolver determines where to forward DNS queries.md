route 53의 동작 방식은 다음과 같다.
1. rule 내 존재하는 doamin인지 확인한다. 매칭되는 rule이 있는 경우 resolver는 outbound endpoint ip주소로 쿼리를 forwarding한다. outbound endpoint는 사용자 네트워크의 resolver로 쿼리를 forwarding한다.
2. resolver endpoint는 autodefined "." rule에 따라 DNS 쿼리를 forwarding한다. 어떤 rule에도 매칭되지 않는 경우 resolver는 autodefined "." (dot) rule에 따라 쿼리를 forwarding한다. 해당 rule은 AWS 내부 domain, private hosted zone에 정의된 reocrd에는 예외적으로 적용되지 않는다. 이 rule을 통해 resolver는 쿼리를 public name server로 forwarding한다.
3. resolver는 쿼리에 대해 응답한다.