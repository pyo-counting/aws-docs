route 53 resolver는 public record, aws vpc 고유 DNS 이름, aws route 53 private hosted zone에 대한 재귀적 DNS 쿼리를 수행하며 기본적으로 모든 vpc에서 사용할 수 있다.

> **Note**  
> route 53 resolver는 이전에 Amazon DNS server로 불렸으나, resolver rule, inbound & outbound endpoint가 추가되면서 이름이 바뀌었다.

vpc는 vpc cidr + 2 주소를 사용해 route 53 resolver에 연결한다. 이는 az 내의 route 53 resolver에 연결되는 주소다.

route 53 resolver는 아래 DNS 쿼리에 대해 응답한다.
- ec2 인스턴스에 대한 local vpc 도메인 이름
- private hosted zone의 record
- public 도메인. route 53 resolver는 이를 위해 internet을 사용해 public record에 대해 recursive lookup을 수행한다.

vpc와 on-premise 리소스를 모두 사용할 경우(VPN 또는 DX를 사용한 hybrid cloud 환경) resvoler endpoint와 (옵션)forwarding rule을 사용할 수 있다.
- inbound resolver endpoint를 사용해 on-premise -> vpc DNS 쿼리를 할 수 있다.
- outbound resolver endpoint를 사용해 vpc -> on-premise DNS 쿼리를 할 수 있다.
- resolver rule을 사용해 각 도메인에 대해 forwarding rule을 만들 수 있다.

아래 그림은 resolver endpoint를 사용한 hybrid DNS resolution을 나타낸다(1개의 az로 간소화).

![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/Resolver-routing.png)

**Outbound (solid arrows 1–5)**
1. An Amazon EC2 instance needs to resolve a DNS query to the domain internal.example.com. The authoritative DNS server is in the on-premises data center. This DNS query is sent to the VPC+2 in the VPC that connects to Route 53 Resolver.
2. A Route 53 Resolver forwarding rule is configured to forward queries to internal.example.com in the on-premises data center.
3. The query is forwarded to an outbound endpoint.
4. The outbound endpoint forwards the query to the on-premises DNS resolver through a private connection between AWS and the data center. The connection can be either AWS Direct Connect or AWS Site-to-Site VPN, depicted as a virtual private gateway.
5. The on-premises DNS resolver resolves the DNS query for internal.example.com and returns the answer to the Amazon EC2 instance via the same path in reverse.

**Inbound (dashed arrows a–d)**
1. A client in the on-premises data center needs to resolve a DNS query to an AWS resource for the domain dev.example.com. It sends the query to the on-premises DNS resolver.
2. The on-premises DNS resolver has a forwarding rule that points queries to dev.example.com to an inbound endpoint.
3. The query arrives at the inbound endpoint through a private connection, such as AWS Direct Connect or AWS Site-to-Site VPN, depicted as a virtual gateway.
4. The inbound endpoint sends the query to Route 53 Resolver, and Route 53 Resolver resolves the DNS query for dev.example.com and returns the answer to the client via the same path in reverse.