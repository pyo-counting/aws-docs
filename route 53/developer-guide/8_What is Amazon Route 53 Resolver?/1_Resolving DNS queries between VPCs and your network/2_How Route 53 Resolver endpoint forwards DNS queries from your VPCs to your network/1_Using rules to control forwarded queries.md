rule은 route 53 resolver endpoint(outbound)가 사용자 네트워크의 DNS resolver로 전달할 DNS 쿼리와 route 53 resolver가 직접 응답할 쿼리를 제어한다.

rule을 분류하는 여러가지 방법이 있다. 첫 번째는 누가 만들었는지
- autodefined rule: resolver는 autodefined rule을 자동으로 생성하고 vpc에 연결한다. 대부분의 autodefined rule은 AWS 관련 dmain과 관련있다.
- custom rule: 현재는 forwarding rule(또는 conditional forwarding rule), delegation rule만 사용자가 생성할 수 있다.
    - forwarding rule: resolver가 DNS 쿼리를 사용자 네트워크로 forwarding 한다. 만약 autodefined rule과 겹친다면 forwarding rule을 우선적으로 따른다.
    - delegation rule: DNS 응답의 NS record(delegation record)가 delegation rule에 지정된 도메인 이름과 일치하면 resolver는 위임된 네임 서버에 쿼리를 forward한다.

두 번째는 룰의 목적이 무엇인지
- conditional forwarding rule: 특정 domain에 사용자 네트워크로 forwarding
- system rule: forwarding rule을 선택적으로 덮어쓸 수 있다. 기본적으로 forwarding rule은 domain 이름과 모든 sub-domain에 적용된다. 만약 일부 sub-doamin에 대해서는 forwarding 하고 싶지 않을 경우 system rule을 만들면 된다.
- recursive rule: resolver는 Internet Resolver라는 rule을 자동 생성한다. 이 rule을 통해 resolver는 custom rule, autodefined rule에 정의되지 않은 쿼리에 대해 recursive하게 동작하도록 한다.