NAT gateway를 생성할 때 두 가지 타입을 설정할 수 있다.

- public: public subnet에 public NAT gateway를 생성하고 eip를 할당해야 한다. 그리고 NAT gateway의 트래픽을 internet gateway로 라우팅할 수 있도록 라우트를 추가한다. NAT gateway의 트래픽을 transit gateway, virtual private gateway으로 라우팅할 수 있다. 이 때는 eip를 사용하지 않으며 NAT gateway의 private ipv4 주소만 사용된다.
- private: private subnet의 인스턴스를 다른 vpc, on-premise network에 연결할 수 있다. NAT gateway의 트래픽을 transit gateway, virtual private gateway으로 라우팅할 수 있다. private NAT gateway에 eip를 연결할 수 없다. 만약 private NAT gateway 트래픽을 internet gateway로 라우팅하면 internet gateway는 트래픽을 drop한다.

private, public NAT gateway 모두 인스턴스의 private ipv4 주소를 NAT gateway의 private ipv4 주소로 변환한다. public NAT gateway의 경우는 추가적으로 internet gateway가 NAT gateway의 private ipv4 주소를 eip로 변환한다.

## [NAT gateway basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-basics)
각 az에 NAT gateway를 생성하고 각 az의 리소스는 해당 az의 NAT gateway를 사용하는 것을 권장한다.

NAT gateway의 특성은 다음과 같다.
- TCP, UDP, ICMP를 지원한다.
- NAT gateways are supported for IPv4 or IPv6 traffic. For IPv6 traffic, NAT gateway performs NAT64. By using this in conjunction with DNS64 (available on Route 53 resolver), your IPv6 workloads in a subnet in Amazon VPC can communicate with IPv4 resources. These IPv4 services may be present in the same VPC (in a separate subnet) or a different VPC, on your on-premises environment or on the internet.
- A NAT gateway supports 5 Gbps of bandwidth and automatically scales up to 100 Gbps. If you require more bandwidth, you can split your resources into multiple subnets and create a NAT gateway in each subnet.
- A NAT gateway can process one million packets per second and automatically scales up to ten million packets per second. Beyond this limit, a NAT gateway will drop packets. To prevent packet loss, split your resources into multiple subnets and create a separate NAT gateway for each subnet.
- Each IPv4 address can support up to 55,000 simultaneous connections to each unique destination. A unique destination is identified by a unique combination of destination IP address, the destination port, and protocol (TCP/UDP/ICMP). You can increase this limit by associating up to 8 IPv4 addresses to your NAT Gateways (1 primary IPv4 address and 7 secondary IPv4 addresses). You are limited to associating 2 Elastic IP addresses to your public NAT gateway by default. You can increase this limit by requesting a quota adjustment. For more information, see Elastic IP addresses.
- You can pick the private IPv4 address to assign to the NAT gateway or have it automatically assigned from the IPv4 address range of the subnet. The assigned private IPv4 address persists until you delete the private NAT gateway. You cannot detach the private IPv4 address and you cannot attach additional private IPv4 addresses.
- NAT gateway에 security group을 사용할 수 없다.
- network ACL를 사용해 NAT gateway의 inbound / outbound 트래픽을 제어할 수 있다. NAT gateway는 1024 - 65535 포트를 사용한다.
- You can't route traffic to a NAT gateway through a VPC peering connection, a Site-to-Site VPN connection, or AWS Direct Connect. A NAT gateway cannot be used by resources on the other side of these connections.
- NAT gateways support traffic with a maximum transmission unit (MTU) of 8500, but it's important to note the following:
    - To prevent potential packet loss when communicating with resources over the internet using a public NAT gateway, the MTU setting for your EC2 instances should not exceed 1500 bytes. For more information about checking and setting the MTU on an instance, see Check and set the MTU on your Linux instance in the Amazon EC2 User Guide for Linux Instances.
    - NAT gateways support Path MTU Discovery (PMTUD) via FRAG_NEEDED ICMPv4 packets and Packet Too Big (PTB) ICMPv6 packets.
    - NAT gateways enforce Maximum Segment Size (MSS) clamping for all packets. For more information, see RFC879.
- secondary private / public ipv4 주소를 할당할 수 있다.