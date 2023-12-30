## [Subnet sizing for IPv4](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html#subnet-sizing-ipv4)
subnet 마다 AWS가 예약한 ip가 있어 사용자가 사용할 수 없다. 아래는 subnet cidr 10.0.0.0/24에 대한 예시다.

- `10.0.0.0`: 네트워크 주소
- `10.0.0.1`: vpc router 주소
- `10.0.0.2`: dns server의 주소를 위해 vpc 네트워크 주소 + 2를 사용한다. subnet이 여러개일 경우 primary cidr의 주소를 사용한다. 추가적으로 AWS는 각 subnet의 두 번째 주소를 예약한다. 
- `10.0.0.3`: aws의 예약 주소
- `10.0.0.255`: 네트웤 브로드캐스트 주소. 하지만 vpc 내에서는 브로드캐스트를 지원하지 않으며 단순히 예약된 주소