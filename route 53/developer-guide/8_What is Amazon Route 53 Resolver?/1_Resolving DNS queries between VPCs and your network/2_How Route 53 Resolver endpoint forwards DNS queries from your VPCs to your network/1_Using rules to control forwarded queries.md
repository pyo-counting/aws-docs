rule은 route 53 resolver endpoint가 사용자 네트워크의 DNS resolver로 전달할 DNS 쿼리와 route 53 resolver가 직접 응답할 쿼리를 제어한다.
rule은 route 53 resolver endpoint(inbound / outbound)가 DNS 쿼리에 대해 사용자 네트워크로 또는 자신이 응답할지에 대한 규칙을 정의한다.

rule을 분류하는 여러가지 방법이 있다. 첫 번째는 누가 만들었는지
- Autodefined rules: resolver는 미리 정의된 rule을 자동으로 생성하고 vpc에 연결한다. 대부분은 resolver가 쿼리에 대한 응답을 위해 aws 할당 domain에 적용된다.
- Custom rules: 현재는 forwarding rule(또는 conditional forwarding rule), delegation rule만 사용자가 생성할 수 있다. forwarding rule은 resolver가 DNS 쿼리를 사용자 네트워크로 forwarding 한다. 만약 autodefined rule과 겹친다면 forwarding rule을 우선적으로 따른다. delegation rule은 규칙에 포함된 delegation records가 네트워크상의 resolver가 응답으로 받은 NS record와 일치할 경우 해당 DNS 쿼리를 forwarding한다.

두 번째는 룰의 목적이 무엇인지
- Conditional forwarding rules: 특정 domain에 사용자 네트워크로 forwarding
- System rules: forwarding rule을 선택적으로 덮어쓸 수 있다. 기본적으로 forwarding rule은 domain 이름과 모든 sub-domain에 적용된다. 만약 일부 sub-doamin에 대해서는 forwarding 하고 싶지 않을 경우 system rule을 만들면 된다.
- Recursive rule: resolver는 Internet Resolver라는 rule을 자동 생성한다. 이 rule을 통해 resolver는 custom rule, autodefined rule에 정의되지 않은 쿼리에 대해 recursive하게 동작하도록 한다.