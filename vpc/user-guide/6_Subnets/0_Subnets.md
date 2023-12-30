### [Subnet types](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html#subnet-basics)
subnet의 타입은 해당 subnet의 라우팅 설정에 따라 달라진다.
- public subnet: internet gateway에 대한 직접적인 라우트가 있는 subnet.
- private subnet: internet gateway에 대한 직접적인 라우트가 없는 subnet.
- vpn-only subnet: virtual private gateway을 통해 Site-to-Site VPN connection에 대한 라우트가 있는 subnet.
- isolated subnet: vpc 외부로의 라우트가 없는 subnet

## [Subnet security](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html#subnet-security)
보안을 위해 private subnet을 사용하는 것을 권장한다.

security group는 인스턴스의 inbound / outbound 트래픽을 제어한다. network acl은 subnet 레벨에서 inbound / outbound 트래픽을 제어한다. 대부분의 경우 security group으로 충분하지만 network acl을 사용해 추가적인 보안을 가질 수 있다.

모든 subnet은 network acl과 연결되어 있다. subnet은 기본적으로 기본 network acl에 연결된다. 기본 network acl은 모든 inbound / outbound 트래픽을 허용한다.