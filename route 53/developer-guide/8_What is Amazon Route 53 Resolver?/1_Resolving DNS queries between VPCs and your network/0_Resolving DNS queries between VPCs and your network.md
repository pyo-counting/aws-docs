resolver는 on-premise와의 DNS 쿼리를 위한 endpoint를 포함한다.

> **NOTE**  
> on-premise 환경에서 직접 vpc +2 주소를 사용해 DNS 쿼리를 수행하는 것은 지원하지 않는 기능이다. 대신 inbound endpoint를 사용하는 것을 권장한다. 하지만 !! 테스트 시, TGW 환경 에서 VPC-1의 EC2에서 VPC-2의 CIDR + 2 주소로 DNS 쿼리가 가능한 것을 테스트(`nslookup` 명령어) 했다.

route53 resolver와 사용자 네트워크에 있는 resolver을 forwarding rule을 사용해 통합할 수 있다. 사용자 네트워크는 vpc에서 접근할 수 있어야하며 예시는 다음과 같다.
- vpc
- peered vpc
- aws dx, vpn, nat gateway를 통해 연결도니 on-premise network

**Inbound endpoint: DNS resolvers on your network can forward DNS queries to Route 53 Resolver via this endpoint**  
inbound endpoint는 두 가지 유형이 있다. IP 주소로 전달하는 'default inbound endpoint'와 사용자 DNS 서버가 관리하는 특정 subdomain의 관리 정책을 Route 53 Resolver로 위임하는 'delegation inboind endpoint'가 있다. inbound endpoint는 사용자의 DNS resolver들이 EC2 인스턴스 또는 Route 53 private hosted zone의 record와 같은 AWS 리소스에 대한 도메인 이름을 쉽게 resolve하도록 허용한다.

**Outbound endpoint: Resolver conditionally forwards queries to resolvers on your network via this endpoint**  
DNS query를 사용자 DNS resolver로 forwarding하기 위해 resolver rule을 만든다. resolver rule은 forwarding 대상 domain 이름과 사용자 DNS resolver의 ip주소를 포함한다. DNS query가 여러 rule에 매칭되는 경우 resolver는 더 정확하게 매칭되는 rule을 선택하고 해당 rule에 명시된 사용자 DNS resolver ip로 DNS query를 forwarding한다.

vpc처럼 resolver도 regional하다.

소유하지 않은 vpc에 resolver endpoint를 생성할 수 없다. vpc owner만 생성이 가능하다.

forwarding을 하기 위해 resolver의 inbound / outbound endpoint를 생성해야한다. endpoint 생성 시 ip 주소를 할당할 수 있으며 각 ip 주소에 대해 vpc elastic network interface가 생성된다.

아래 그림은 사용자 네트워크 -> route 53 resolver endpoint DNS 쿼리 예시다.
![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/Resolver-inbound-endpoint.png)

아래 그림은 ec2 -> 사용자 네트워크 DNS 쿼리 예시다.
![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/Resolver-outbound-endpoint.png)
