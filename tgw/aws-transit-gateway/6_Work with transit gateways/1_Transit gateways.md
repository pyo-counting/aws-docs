transit gatewy에 vpc, vpn을 attach하고 트래픽을 routing할 수 있다. transit gateway는 aws 게정 간 동작할 수 있으며 ram을 이용해 다른 aws 게정과 공유할 수 있다. 계정 owner는 transit gateway에 vpc를 attach할 수 있다. 두 aws 계정 사용자는 해당 attachment를 삭제할 수 있다.

You can enable multicast on a transit gateway, and then create a transit gateway multicast domain that allows multicast traffic to be sent from your multicast source to multicast group members over VPC attachments that you associate with the domain.

transit gateway는 dynamic, static route를 제공한다. transit gateway peering은 static route만 제공한다. However, you can't add a static route that points to a peering between two transit gateways in the same Region.

You can optionally associate one or more IPv4 or IPv6 CIDR blocks with your transit gateway. You specify an IP address from the CIDR block when you establish a Transit Gateway Connect peer for a Transit Gateway Connect attachment. You can associate any public or private IP address range, except for addresses in the 169.254.0.0/16 range, and ranges that overlap with addresses for your VPC attachments and on-premises networks. For more information about IPv4 and IPv6 CIDR blocks, see VPCs and subnets in the Amazon VPC User Guide.