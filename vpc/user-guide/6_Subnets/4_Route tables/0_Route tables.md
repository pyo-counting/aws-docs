## [Route table concepts](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html#RouteTables)
- main route table: vpc 내 기본적으로 제공되는 라우트 테이블. subnet에 대해 명시적으로 route table을 연결하지 않으면 main route table이 연결된다.
- subnet route table: subnet에 연결된 route table
- local route: vpc 내에서의 통신을 위한 기본 route. 모든 route table은 기본적으로 갖는 route
- gateway route table: internet gateway, virtual private gateway와 연결된 route table
- transit gateway route table: transit gateway와 연결된 route table

### [Routes](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html#subnet-route-tables)
Rules and considerations
- longest prefix match 규칙을 사용해 라우팅한다.
- 169.254.168.0/22, 정확하게 일치하는 ip에 대한 route를 추가할 수 없다. 169.254.168.0/22는 link-local address로 AWS 서비스에서 사용하기 위해 예약되었다. 예를 들어 ec2 서비스의 경우 Instance Metadata Service (IMDS), the Amazon DNS server에 사용한다. You can use a CIDR block that is larger than but overlaps 169.254.168.0/22, but packets destined for addresses in 169.254.168.0/22 will not be forwarded.

## [Route priority](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html#route-tables-priority)
longest prefix match을 사용한다. 

### Route priority and propagated routes

### Route priority and prefix lists