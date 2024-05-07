아래는 transit gateway 디자인에 대한 beset practice다.
- vpc attachment를 위해 별도의 subnet을 이용한다. subnet의 크기는 가장 작은 `/28` cidr를 사용한다.
    - transit gateway subnet에 대한 network ACL를 통해 트래픽을 제어할 수 있다,
    - Depending on your traffic flow, you can apply network ACLs to your workload subnets.
- transit gateway와 연결된 subnet을 위해 1개의 network ACL를 생성하고 연결한다.
- 여러 route table이 필요하지 않다면 transit gateway와 연결된 subnet을 위해 1개의 subnet route table을 연결한다.
- Use Border Gateway Protocol (BGP) Site-to-Site VPN connections. If your customer gateway device or firewall for the connection supports multipath, enable the feature.
- Enable route propagation for AWS Direct Connect gateway attachments and BGP Site-to-Site VPN attachments.
- When migrating from VPC peering to use a transit gateway. An MTU size mismatch between VPC peering and the transit gateway might result in some packets dropping for asymmetric traffic. Update both VPCs at the same time to avoid jumbo packets dropping due to size mismatches.
- transit gateway는 네트워크 트래픽에 따라 scaling 되며, 고가용성을 제공하기 떄문에 여러 transit gateway를 생성할 필요가 없다.
- transit gateway route table에 대한 개수를 제한한다.
- 이중화를 위해 각 region에 transit gateway를 사용한다.
- 여러 transit gateway를 관리하는 경우 ASN(Autonomous System Number)를 사용한다. region 간 peering도 가능하다.