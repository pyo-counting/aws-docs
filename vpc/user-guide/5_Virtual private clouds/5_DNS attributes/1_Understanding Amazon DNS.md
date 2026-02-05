DNS hostname은 컴퓨터의 이름을 고유하게 식별하는 정보다: host name + domain name으로 구성된다.

## [Amazon DNS server](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#AmazonDNS)
Route 53 Resolver(Amazon DNS server 또는 AmazonProvidedDNS로 불림)는 region 내 az 별로 내장된 DNS Resolver service다. Route 53 Resolver는 169.254.169.253 (IPv4), fd00:ec2::253 (IPv6), VPC + 2에 위치한다. vpc 내 리소스는 link local address를 사용해 DNS 쿼리를 수행한다. DNS query는 Route 53 Resolver로 비공개적으로 전송되기 때문에 네트워크 상에 표시되지 않는다.

vpc 내에서 instance를 시작하면 해당 instance에 private DNS 호스트 이름이 할당받는다. 인스턴스에 public IPv4 주소를 할당한 경우 VPC DNS attribute가 활성화 됐다면 public DNS 호스트 이름도 할당받는다.

### [Rules and considerations](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#AmazonDNS)
AWS DNS server를 사용할 떄 아래 규칙이 적용된다.
- AWS DNS server를 사용할 때 acl, security group을 통해 AWS DNS server에 대한 in / out 트래픽을 제어할 수 없다.
- AWS route 53 resolver는 재귀적 DNS 쿼리만 제공한다.

## [DNS hostnames](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-hostnames)
AWS instance는 항상 private ipv4 주소, private ipv4 주소와 관련된 DNS hostname을 할당받는다. 만약 public ipv4 주소를 갖고 있는 경우에는 vpc attribute에 따라 public ipv4 주소와 관련된 DNS hostname도 할당받는다.

AWS 제공 DNS 서버를 활성화하면 DNS hostname이 할당되고 아래 규칙을 통해 resolve된다.

- Private IPv4 DNS name: 동일한 vpc의 인스턴스 간 통신에 전용 ip DNS 이름(ipv4 전용) hostname을 사용할 수 있다. 인스턴스가 동일한 AWS region에 있고 다른 인스턴스의 hostname이 RFC 1918에 정의된 범위(10.0.0 - 10.255.255(10/8), 172.16.0 - 172.31.255.255(172.16/12) 및 192.168.0 - 192.168.255(192.16/16))에 있다면 다른 vpc의 인스턴스의 ip DNS 이름(ipv4 전용) hostname을 확인할 수 있다.
- Public IPv4 DNS name: public ipv4 DNS hostname은 `ec2-${public-ipv4-address}.${region}.compute.amazonaws.com` 형식을 갖는다. Amazon DNS server는 인스턴스의 외부 네트워크에서는 public ip로 인스턴스의 내부 네트워크에서는 private ip로 resolve한다.
- Private resource DNS name:

## [DNS attributes in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support)
아래 vpc attribute에 따라 vpc 내 DNS 지원에 대한 차이점이 있다. 두 속성이 모두 활성화된 경우 vpc 내 인스턴스 생성 시 public ipv4 주소 또는 elastic ip를 항당한 경우 public hostname을 할당받는다. 이미 생성된 인스턴스에 대해서는 두 속성을 활성화했을 때 할당받는다.
- DNS options:
    - Enable DNS hostnames: ec2 인스턴스가 public ip에 대해 public DNS hostname을 할당받을지 여부. default vpc를 제외하고 기본 값은 false
    - Enable DNS resolution: vpc 내에서 route 53 resolver(또는 DNS server)을 통한 DNS resolution을 사용할지 여부. 기본 값은 true

- 두 옵션이 모두 true라면 아래 경우가 발생한다.
    - public IP 주소가 있는 인스턴스는 public DNS hostname을 할당받는다.
    - Amazon Route 53 Resolver 서버는 Amazon-provided private DNS hostname을 resolve할 수 있다.
- 두 옵션 중 하나라도 false라면 아래 경우가 발생한다.
    - public ip가 있는 인스턴스에 대해 public DNS hostname을 할당 받지 못한다.
    - Amazon Route 53 Resolver는 Amazon-provided private DNS hostname을 resolve하지 못한다.
    - DHCP option set에 사용자 지정 도메인 이름이 있는 경우 인스턴스는 사용자 지정 프라이빗 DNS 호스트 이름을 할당 받는다. Route 53 Resolver 서버를 사용하지 않는 경우, 사용자 지정 DNS가 호스트 이름에 대해 리졸빙할 수 있어야 한다.

## [Private hosted zones](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-private-hosted-zones)
route 53의 private hosted zone을 사용해 사용자 정의 DNS 도메인을 사용할 경우 enableDnsHostnames, enableDnsSupport 설정을 모두 활성화해야 한다.

## [DNS quotas](https://docs.aws.amazon.com/vpc/latest/userguide/AmazonDNS-concepts.html#vpc-dns-limits)
link local address를 사용하는 서비스에는 초당 1024 PPS(packet per second) 제한이 있다. 이 제한에는 Route 53 Resolver DNS 쿼리, 인스턴스 메타데이터 서비스(IMDS) 요청, Amazon Time Service 네트워크 시간 프로토콜(NTP) 요청, Windows 라이선싱 서비스(Microsoft Windows 기반 인스턴스용) 요청의 총합이 포함된다. 해당 quota는 늘릴 수 없다.

Route 53 Resolver에서 지원하는 초당 DNS 쿼리 수는 쿼리 유형, 응답 크기 및 사용 중인 프로토콜에 따라 다르다. 확장 가능한 DNS 아키텍처에 대한 자세한 내용과 권장 사항은 [AWS Hybrid DNS with Active Directory](https://d1.awsstatic.com/whitepapers/aws-hybrid-dns-with-active-directory.pdf) 기술 가이드를 참고한다.

quota에 도달하면 Route 53 Resolver는 트래픽을 거부한다. quota에 도달하는 원인 중 일부는 DNS 스로틀링 문제 또는 Route 53 Resolver 네트워크 인터페이스를 사용하는 인스턴스 메타데이터 쿼리일 수 있다.

## [Private hosted zones](https://docs.aws.amazon.com/vpc/latest/userguide/AmazonDNS-concepts.html#vpc-private-hosted-zones)

Amazon Route 53의 private hosted zone에 정의된 사용자 지정 DNS 도메인 이름을 사용하는 경우 enableDnsHostnames와 enableDnsSupport vpc attribute를 모두 true로 설정해야 한다.