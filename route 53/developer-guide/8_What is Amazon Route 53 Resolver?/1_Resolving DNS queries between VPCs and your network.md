resolver는 on-premise와의 DNS 쿼리를 위한 endpoint를 제공할 수 있다.

> **NOTE**  
> on-premise 환경에서 직접 vpc +2 주소를 사용해 DNS 쿼리를 수행하는 것은 지원하지 않는 기능이다.

route53 resolver와 사용자 네트워크에 있는 resolver을 forwarding rule을 사용해 통합할 수 있다. 사용자 네트워크는 vpc에서 접근할 수 있어야하며 예시는 다음과 같다.
- vpc
- peered vpc
- aws dx, vpn, nat gateway를 통해 연결도니 on-premise network

**Inbound endpoint: DNS resolvers on your network can forward DNS queries to Route 53 Resolver via this endpoint**  
사용자의 DNS resolver는 inbound endpoint를 통해 route 53 resolver로 DNS query를 forwarding할 수 있다.

**Outbound endpoint: Resolver conditionally forwards queries to resolvers on your network via this endpoint**  
DNS query를 사용자 DNS resolver로 forwarding하기 위해 resolver rule을 만든다. resolver rule은 forwarding 대상 domain 이름과 사용자 DNS resolver의 ip주소를 포함한다. DNS query가 여러 rule에 매칭되는 경우 resolver는 더 정확하게 매칭되는 rule을 선택하고 해당 rule에 명시된 사용자 DNS resolver ip로 DNS query를 forwarding한다.

vpc처럼 resolver도 regional하다.

forwarding을 하기 위해 resolver의 inbound / outbound endpoint를 생성해야한다. endpoint 생성 시 ip 주소를 할당할 수 있으며 각 ip 주소에 대해 vpc elastic network interface가 생성된다.

아래 그림은 사용자 네트워크 -> route 53 resolver endpoint DNS 쿼리 예시다.
![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/Resolver-inbound-endpoint.png)

아래 그림은 ec2 -> 사용자 네트워크 DNS 쿼리 예시다.
![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/Resolver-outbound-endpoint.png)

## [How DNS resolvers on your network forward DNS queries to Route 53 Resolver endpoints](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-overview-DSN-queries-to-vpc.html#resolver-overview-forward-network-to-vpc)
When you want to forward DNS queries from your network to Route 53 Resolver endpoints in an AWS Region, you perform the following steps:

1. You create a Route 53 Resolver inbound endpoint in a VPC and specify the IP addresses that the resolvers on your network forward DNS queries to. For each IP address that you specify for the inbound endpoint, Resolver creates a VPC elastic network interface in the VPC where you created the inbound endpoint.
2. You configure resolvers on your network to forward DNS queries for the applicable domain names to the IP addresses that you specified in the inbound endpoint. For more information, see Considerations when creating inbound and outbound endpoints.

Here's how Resolver resolves DNS queries that originate on your network:

1. A web browser or another application on your network submits a DNS query for a domain name that you forwarded to Resolver.
2. A resolver on your network forwards the query to the IP addresses in your inbound endpoint.
3. The inbound endpoint forwards the query to Resolver.
4. Resolver gets the applicable value for the domain name in the DNS query, either internally or by performing a recursive lookup against public name servers.
5. Resolver returns the value to the inbound endpoint.
6. The inbound endpoint returns the value to the resolver on your network.
7. The resolver on your network returns the value to the application.
8. Using the value that was returned by Resolver, the application submits an HTTP request, for example, a request for an object in an Amazon S3 bucket.

Creating an inbound endpoint doesn't change the behavior of Resolver, it just provides a path from a location outside the AWS network to Resolver.

## [How Route 53 Resolver endpoint forwards DNS queries from your VPCs to your network](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-overview-DSN-queries-to-vpc.html#resolver-overview-forward-vpc-to-network)

### Using rules to control which queries are forwarded to your network
rule은 route 53 resolver endpoint가 DNS 쿼리에 대해 사용자 네트워크로 또는 자신이 응답할지에 대한 규칙을 정의한다.

rule을 분류하는 여러가지 방법이 있다. 첫 번째는 누가 만들었는지
- Autodefined rules: resolver는 미리 정의된 rule을 자동으로 생성하고 vpc에 연결한다. 대부분은 resolver가 쿼리에 대한 응답을 위해 aws 할당 domain에 적용된다.
- Custom rules: 현재는 forwarding rule(또는 conditional forwarding rule)만 사용자가 생성할 수 있다. 이는 resolver가 DNS 쿼리를 사용자 네트워크로 forwarding 한다. 만약 autodefined rule과 겹친다면 forwarding rule을 우선적으로 따른다.

두 번째는 룰의 목적이 무엇인지
- Conditional forwarding rules: 특정 domain에 사용자 네트워크로 forwarding
- System rules: forwarding rule을 선택적으로 덮어쓸 수 있다. 기본적으로 forwarding rule은 domain 이름과 모든 sub-domain에 적용된다. 만약 일부 sub-doamin에 대해서는 forwarding 하고 싶지 않을 경우 system rule을 만들면 된다.
- Recursive rule: resolver는 Internet Resolver라는 rule을 자동 생성한다. 이 rule을 통해 resolver는 custom rule, autodefined rule에 정의되지 않은 쿼리에 대해 recursive하게 동작하도록 한다.

### How Resolver determines whether the domain name in a query matches any rules
route 53 resolver는 DNS 쿼리 내 존재하는 domain과 rule의 domain을 비교해 아래 규칙을 적용한다.
- 정확하게 매칭되는지 여부 확인
- 쿼리 domain이 규칙 domain의 sub-doamin인지 여부 확인

### How Resolver determines where to forward DNS queries
route 53의 동작 방식은 다음과 같다.
1. rule 내 존재하는 doamin인지 확인한다. 매칭되는 rule이 있는 경우 resolver는 outbound endpoint ip주소로 쿼리를 forwarding한다. outbound endpoint는 사용자 네트워크의 resolver로 쿼리를 forwarding한다.
2. resolver endpoint는 autodefined "." rule에 따라 DNS 쿼리를 forwarding한다. 어떤 rule에도 매칭되지 않는 경우 resolver는 autodefined "." (dot) rule에 따라 쿼리를 forwarding한다. 해당 rule은 AWS 내부 domain, private hosted zone에 정의된 reocrd에는 예외적으로 적용되지 않는다. 이 rule을 통해 resolver는 쿼리를 public name server로 forwarding한다.
3. resolver는 쿼리에 대해 응답한다.

## [Considerations when creating inbound and outbound endpoints](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-overview-DSN-queries-to-vpc.html#resolver-choose-vpc)

### Number of inbound and outbound endpoints in each Region

### Using the same VPC for inbound and outbound endpoints

### Inbound endpoints and private hosted zones

### VPC peering

### IP addresses in shared subnets

### Connection between your network and the VPCs that you create endpoints in
사용자 네트워크와 resolver endpoint가 생성되는 vpc 사이에 다음 중 하나의 연결이 필요하다.
- inbound endpoint: AWS Direct Connect 또는 VPN connection
- outbound endpoint: AWS Direct Connect, VPN connection, NAT gateway

