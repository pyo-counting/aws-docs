## [Create a DHCP option set](https://docs.aws.amazon.com/vpc/latest/userguide/DHCPOptionSet.html#CreatingaDHCPOptionSet)
DHCP option set을 생성한 이후에는 수정이 불가하다.

DHCP option set은 아래 설정을 제공한다.
- `Domain name`: ${REGION}.compute.internal(기본 값). client가 hostname을 resolving할 때 사용해야하는 도메인을 설정한다. ec2 내 /etc/resolv.conf 확인 시 아래 search domain 확인
    ```
    search ap-northeast-2.compute.internal
    ```
- `Domain name servers`: AmazonProvidedDNS(기본 값). client가 사용할 DNS server. AmazonProvidedDNS는 AWS에서 제공하는 DNS server(Amazon Route 53 Resolver)
- `NTP servers`: NTP server의 ip Amazon Time Sync Service의 ip 주소는 169.254.169.123
- `NetBIOS name servers`:
- `NetBIOS node type`: