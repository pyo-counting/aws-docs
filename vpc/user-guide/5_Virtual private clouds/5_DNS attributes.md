DNS hostname은 컴퓨터의 이름을 고유하게 식별하는 정보다: host name + domain name으로 구성된다.

AWS는 vpc를 위한 DNS server를 제공한다.

## [Amazon DNS server](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#AmazonDNS)
Route 53 Resolver(Amazon DNS server 또는 AmazonProvidedDNS로 불림)는 region 내 az 별로 내장된 DNS Resolver service다. Route 53 Resolver는 169.254.169.253 (IPv4), fd00:ec2::253 (IPv6), VPC + 2에 위치한다.

### [Rules and considerations](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#AmazonDNS)
AWS DNS server를 사용할 떄 아래 규칙이 적용된다.

- AWS DNS server를 사용할 때 acl, security group을 통해 AWS DNS server에 대한 in / out 트래픽을 제어할 수 없다.
- AWS route 53 resolver는 재귀적 DNS 쿼리만 제공한다.

## [DNS hostnames](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-hostnames)
AWS instance는 항상 private ipv4 주소, private ipv4 주소와 관련된 DNS hostname을 할당받는다. 만약 public ipv4 주소를 갖고 있는 경우 vpc 설정에 따라 public ipv4 주소와 관련된 DNS hostname을 할당받는다.

AWS 제공 DNS 서버를 활성화하면 DNS hostname이 할당되고 아래 규칙을 통해 resolve된다.

- Private IP DNS name (ipv4 only): 동일한 vpc의 인스턴스 간 통신에 전용 ip DNS 이름(ipv4 전용) hostname을 사용할 수 있다. 인스턴스가 동일한 AWS region에 있고 다른 인스턴스의 hostname이 RFC 1918에 정의된 범위(10.0.0 - 10.255.255(10/8), 172.16.0 - 172.31.255.255(172.16/12) 및 192.168.0 - 192.168.255(192.16/16)에 있다면 다른 vpc의 인스턴스의 ip DNS 이름(ipv4 전용) hostname을 확인할 수 있다.
- Private resource DNS name: 
- Public IPv4 DNS: public ipv4 DNS hostname은 `ec2-${public-ipv4-address}.${region}.compute.amazonaws.com` 형식을 갖는다. Amazon DNS server는 인스턴스의 외부 네트워크에서는 public ip로 인스턴스의 내부 네트워크에서는 private ip로 resolve한다.

## [DNS attributes in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support)
- DNS options:
    - Enable DNS hostnames: ec2 인스턴스가 public ip에 대해 public DNS hostname을 할당받을지 여부
    - Enable DNS resolution: vpc 내에서 route 53 resolver(또는 DNS server)을 통한 DNS resolution을 사용할지 여부

두 옵션 중 하나라도 false라면 아래 경우가 발생한다.
- public ip가 있는 인스턴스에 대해 public DNS hostname을 할당 받지 못한다.
- Amazon Route 53 Resolver는 Amazon-provided private DNS hostname을 resolve하지 못한다.
- Instances receive custom private DNS hostnames if there is a custom domain name in the DHCP options set. If you are not using the Amazon Route 53 Resolver server, your custom domain name servers must resolve the hostname as appropriate.

## [Private hosted zones](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-private-hosted-zones)
route 53의 private hosted zone을 사용해 사용자 정의 DNS 도메인을 사용할 경우 enableDnsHostnames, enableDnsSupport 설정을 모두 활성화해야 한다.