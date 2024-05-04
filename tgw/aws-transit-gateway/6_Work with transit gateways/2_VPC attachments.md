vpc를 transit gateway에 attach할 때 transit gateway가 트래픽을 routing하는 데 사용할 각 az의 subnet을 명시해야 한다. 해당 subnet을 통해 해당 az 내 모든 subnet의 리소스에 트래픽이 도달할 수 있다.

**Limits**
- vpc를 transit gateway에 연결할 때 transit gateway attachment가 없는 az의 리소스는 transit gateway에 도달할 수 없다. subnet route table에 transit gateway로의 route가 있더라도 attachment가 있는 az만 트래픽이 transit gateway로 전달될 수 있다.
- transit gateway에 attach된 vpc 리소스는 transit gateway에 attach된 다른 vpc의 security group에 접근할 수 없다.
- aws route 53의 private hosted zone을 사용해 설정한 attached vpc의 custom dns name에 대한 resolution을 지원하지 않는다. transit gateway에 attach된 모든 vpc의 private hosted zone에 대한 name resolution 설정은 [Centralized DNS management of hybrid cloud with Amazon Route 53 and AWS Transit Gateway](https://aws.amazon.com/ko/blogs/networking-and-content-delivery/centralized-dns-management-of-hybrid-cloud-with-amazon-route-53-and-aws-transit-gateway/)을 참고한다.
- A transit gateway doesn't support routing between VPCs with identical CIDRs. If you attach a VPC to a transit gateway and its CIDR is identical to the CIDR of another VPC that's already attached to the transit gateway, the routes for the newly attached VPC aren't propagated to the transit gateway route table.
- You can't create an attachment for a VPC subnet that resides in a Local Zone. However, you can configure your network so that subnets in the Local Zone can connect to a transit gateway through the parent Availability Zone. For more information, see Connect Local Zone subnets to a transit gateway.
- You can't create a transit gateway attachment using IPv6-only subnets. Transit gateway attachment subnets must also support IPv4 addresses.
- A transit gateway must have at least one VPC attachment before that transit gateway can be added to a route table.