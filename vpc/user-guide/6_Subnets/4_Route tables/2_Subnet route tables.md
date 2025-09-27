vpc는 암묵적 router를 가지고 있으며 사용자는 route table을 통해 제어할 수 있다.

### [Routes](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-route-tables.html#route-table-routes)

Rules and considerations
- longest prefix match 규칙을 사용해 라우팅한다.
- 169.254.168.0/22, 정확하게 일치하는 ip에 대한 route를 추가할 수 없다. 169.254.168.0/22는 link-local address로 AWS 서비스에서 사용하기 위해 예약되었다. 예를 들어 ec2 서비스의 경우 Instance Metadata Service (IMDS), the Amazon DNS server에 사용한다. You can use a CIDR block that is larger than but overlaps 169.254.168.0/22, but packets destined for addresses in 169.254.168.0/22 will not be forwarded.