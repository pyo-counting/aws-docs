NAT gateway를 생성할 때 두 가지 타입을 설정할 수 있다.

- public: public subnet에 public NAT gateway를 생성하고 eip를 할당해야 한다. 그리고 NAT gateway의 트래픽을 internet gateway로 라우팅할 수 있도록 라우트를 추가한다. NAT gateway의 트래픽을 transit gateway, virtual private gateway으로 라우팅할 수 있다. 이 때는 eip를 사용하지 않으며 NAT gateway의 private ipv4 주소만 사용된다.
- private: private subnet의 인스턴스를 다른 vpc, on-premise network에 연결할 수 있다. NAT gateway의 트래픽을 transit gateway, virtual private gateway으로 라우팅할 수 있다. private NAT gateway에 eip를 연결할 수 없다. 만약 private NAT gateway 트래픽을 internet gateway로 라우팅하면 internet gateway는 트래픽을 drop한다.

private, public NAT gateway 모두 인스턴스의 private ipv4 주소를 NAT gateway의 private ipv4 주소로 변환한다. public NAT gateway의 경우는 추가적으로 internet gateway가 NAT gateway의 private ipv4 주소를 eip로 변환한다.

## [NAT gateway basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-basics)
각 az에 NAT gateway를 생성하고 각 az의 리소스는 해당 az의 NAT gateway를 사용하는 것을 권장한다.

NAT gateway의 특성은 다음과 같다.
- TCP, UDP, ICMP를 지원한다.
- NAT gateway에 security group을 사용할 수 없다.
- network ACL를 사용해 NAT gateway의 inbound / outbound 트래픽을 제어할 수 있다. NAT gateway는 1024 - 65535 포트를 사용한다.
- You can't route traffic to a NAT gateway through a VPC peering connection, a Site-to-Site VPN connection, or AWS Direct Connect. A NAT gateway cannot be used by resources on the other side of these connections.
- secondary private / public ipv4 주솔르 할당할 수 있다.