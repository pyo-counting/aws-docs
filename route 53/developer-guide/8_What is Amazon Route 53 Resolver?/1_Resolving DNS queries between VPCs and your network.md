**NOTE**
> on-premise 환경에서 직접 vpc +2 주소를 사용해 DNS 쿼리를 수행하는 것은 지원하지 않는 기능이다.

vpc처럼 resolver도 regional하다.

forwarding을 하기 위해 resolver의 inbound / outbound endpoint를 생성해야한다. endpoint 생성 시 ip 주소를 할당할 수 있으며 각 ip 주소에 대해 vpc elastic network interface가 생성된다.

아래 그림은 사용자 네트워크 -> route 53 resolver endpoint DNS 쿼리 예시다.
![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/Resolver-inbound-endpoint.png)

아래 그림은 ec2 -> 사용자 네트워크 DNS 쿼리 예시다.
![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/Resolver-outbound-endpoint.png)

## [How Route 53 Resolver endpoint forwards DNS queries from your VPCs to your network](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-overview-DSN-queries-to-vpc.html#resolver-overview-forward-vpc-to-network)

### Using rules to control which queries are forwarded to your network
rule은 route 53 resolver가 어떤 DNS 쿼리에 대해서는 사용자 네트워크로 또는 자신이 응답할지에 대한 규칙을 정의한다.

rule을 분류하는 여러가지 방법이 있다. 첫 번째는 누가 만들었는지
- Autodefined rules: resolver는 미리 정의된 rule을 자동으로 생성하고 vpc에 연결한다. 대부분은 resolver가 쿼리에 대한 응답을 위해 aws 할당 domain에 적용된다.
- Custom rules: 현재는 forwarding rule(또는 conditional forwarding rule)만 사용자가 생성할 수 있다. 이는 resolver가 DNS 쿼리를 사용자 네트워크로 forwarding 한다. 만약 autodefined rule과 겹친다면 forwarding rule을 우선적으로 따른다.

두 번째는 룰의 목적이 무엇인지
- Conditional forwarding rules: 특정 domain에 사용자 네트워크로 forwarding
- System rules: forwarding rule을 선택적으로 덮어쓸 수 있다. 기본적으로 forwarding rule은 domain 이름과 모든 sub-domain에 적용된다. 만약 일부 sub-doamin에 대해서는 forwarding 하고 싶지 않을 경우 system rule을 만들면 된다.
- Recursive rule: resolver는 Internet Resolver라는 rule을 자동생성한다. 이 rule을 통해 resolver는 custom rule, autodefined rule에 정의되지 않은 쿼리에 대해 recursive하게 동작하도록 한다.

### How Resolver determines whether the domain name in a query matches any rules
route 53 resolver는 DNS 쿼리 내 존재하는 domain과 rule의 domain을 비교해 아래 규칙을 적용한다.
- 정확하게 매칭되는지 여부 확인
- 쿼리 domain이 규칙 domain의 sub-doamin인지 여부 확인

### How Resolver determines where to forward DNS queries
route 53의 동작 방식은 다음과 같다.
1. rule 내 존재하는 doamin인지 확인한다. 매칭되는 rule이 있는 경우 resolver는 outbound endpoint ip주소로 쿼리를 forwarding한다. outbound endpoint는 사용자 네트워크의 resolver로 쿼리를 forwarding한다.
2. resolver endpoint는 autodefined "." rule에 따라 DNS 쿼리를 forwarding한다. 어떤 rule에도 매칭되지 않는 경우 resolver는 autodefined "." (dot) rule에 따라 쿼리를 forwarding한다. 해당 rule은 AWS 내부 domain, private hosted zone에 정의된 reocrd에는 예외적으로 적용되지 않는다. 이 rule을 통해 resolver는 쿼리를 public name server로 forwarding한다.
3. resolver는 쿼리에 대해 응답한다.