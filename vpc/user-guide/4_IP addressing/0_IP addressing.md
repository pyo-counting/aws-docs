## [Compare IPv4 and IPv6](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#ipv4-ipv6-comparison)
ipv4의 vpc cidr 크기는 /16 - /28

## [Private IPv4 addresses](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#vpc-private-ipv4-addresses)
primary private ip 주소는 인스턴스의 기본 network interface(eht0)에 할당된다. 그리고 DNS hostname을 할당 받는다. IPBN(IP Based Name) 또는 RBN(Resource Bsaed Name) 타입이 있다.

추가적으로 secondary ip에 여러 ip를 할당할 수 있다.

## [Public IPv4 addresses](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#vpc-public-ipv4-addresses)
subnet은 해당 subnet에 인스턴스를 생성할 때 public ip를 자동적으로 할당받을지 여부를 결정하는 설정 값이 있다. 해당 속성이 활성화됐을 때 public ip는 primary network interface (eth0)에 할당된다(실제 인스턴스에 접속해서 ifconfig 명령어로 확인시에는 보이지 않음). 그리고 해당 public ip는 NAT을 통해 private ip에 매핑된다.

vpc의 DNS hostname 특성이 활성화된 경우 public ip가 있는 인스턴스에 대해서는 public DNS hostname을 할당한다. 인스턴스의 네트워크 밖에서는 public DNS hostname가 public ip로 resolv되며 동일 네트워크 안에서는 private ip로 resolv된다.