transit gateway는 regional virtual router로 동작한다. transit gateway를 통한 라우팅은 3 계층에서 이루어지며 packet은 next-hop attachement로 보내진다.

### Equal Cost Multipath routing

## Availability Zones
vpc를 transit gateway에 attach할 때 transit gateway가 vpc로 트래픽을 라우팅할 수 있도록 1개 이상의 az를 활성화 해야한다. az 활성화는 각 az에 대해 1개의 subnet을 명시하면 된다. transit gateway는 명시한 각 subnet의 ip 주소 1개를 사용하는 network interface(type: transit-gateway)를 생성한다. az를 활성화하면(transitgateway attachment 생성) transit gateway는 transit gateway attachment를 통해 해당 subnet, az뿐만 아니라 전체 vpc로 트래픽을 라우팅할 수 있다. 하지만 반대로 해당 transitgateway attachment가 있는 az의 aws resource만 transit gateway에 접근할 수 있다.

기본적으로 transit gateway는 트래픽이 인입되는 transitgateway attachment의 az와 동일한 az의 transitgateway attachment로 traffic을 보낸다. 하지만 목적지 attachment가 존재하지 않는 az에서 트래픽이 유입된 경우 transit gateway는 내부적으로 attachment가 존재하는 랜덤 az로 트래픽을 라우팅한다. 이러한 타입의 cross az 트래픽은 추가 과금이 없다.

**Using appliance mode support**
vpc에서 stateful network appliance 사용을 하는 경우 해당 vpc를 attach할 때 appliance mode를 활성화할 수 있다. 이를 통해 transit gateway는 동일 트래픽 in, out에 대해 해당 vpc의 동일 az로 트래픽을 라우팅한다. 이는 해당 vpc attachment에 대해서만 활성화되는 것이다. 동일 트래픽에 대해 다른 vpc attachment로 보낼 때는 다른 az로 트래픽을 라우팅할 수 있다.

## Routing
vpc의 subnet route table과 transit gateway의 route table에 대해 잘 이해해야 한다. subnet route table에서는 target으로 transit gateway로의 route를 설정할 수 있다. 트래픽은 transit gateway attachment의 network interface 중 동일한 az에 있는 network interface를 통해 transit gateway로 전송된다. 그 다음 transit gateway에서는 해당 transit gateway attachement의 transit gateway route table을 확인해 다음 hop(transit gateway attachment)을 결정한다.

transit gateway route table은 target으로 transit gateway attachament로의 route만 설정할 수 있다. transit gateway route table을 통해 transit gateway attachment로 라우팅이 이루어진다. transit gateway attachment로의 라우팅은 실제 network interface로의 라우팅이다. network interface는 다음 hop을 결정하기 위해 subnet route table을 이용한다.

spoke-1 vpc와 spoke-2 vpc의 tgw를 이용한 트래픽 전송/응답의 흐름은 다음과 같다.
1. spoke-1 vpc / svr sbn: instance-1 eni가 svr sbn의 rt를 확인해 다음 hop(destinationdl tgw)을 찾는다. tgw att eni는 트래픽을 전달받아 tgw로 트래픽을 전달한다(이 때 tgw att eni는 tgw sbn rt를 확인하지 않으며 무조건 다음 홉을 tgw로 바라본다).
2. tgw: spoke-1 vpc의 tgw att rt를 확인해 다음 홉 tgw att을 찾아 해당 tgw att eni로 트래픽을 라우팅한다.
3. spoke-2 vpc / tgw sbn: tgw로부터 받은 트래픽을 tgw att eni는 tgw sbn rt를 참고해 instance-2 eni로 트래픽을 전달한다.
4. spoke-2 vpc / svr sbn: instance-2 eni는 트래픽을 전달받고 응답 트래픽을 전달한다. svr sbn의 rt를 확인해 다음 hop(destinationdl tgw)을 찾는다.  tgw att eni는 트래픽을 전달받아 tgw로 트래픽을 전달한다(이 때 tgw att eni는 tgw sbn rt를 확인하지 않으며 무조건 다음 홉을 tgw로 바라본다).
5. tgw: spoke-2 vpc의 tgw att rt를 확인해 다음 홉 tgw att을 찾아 해당 tgw att eni로 트래픽을 라우팅한다.
6. spoke-1 vpc / tgw sbn: tgw로부터 받은 트래픽을 tgw att eni는 tgw sbn rt를 참고해 instance-1 eni로 트래픽을 전달한다.

실제 vpc, tgw flow log는 다음과 같다.
``` sh
# PG-VPC(ec2 out(SYN))
231805093461 ap-northeast-2 apne2-az3 vpc-013b0b4486425fd0a subnet-0658fffb8ed127d07 i-0856a4b5105174ab7 eni-08326128f74a7d44b 1713372621 1713372652 6 egress 1 172.30.28.147 157.119.32.149 172.30.28.147 157.119.32.149 - - 9316 443 2 120 ACCEPT 2 OK
# PG-VPC(tgwatt in(SYN))
886200405623 ap-northeast-2 apne2-az3 vpc-013b0b4486425fd0a subnet-0de5f8859f49abc49 - eni-094e13ec4ddfda270 1713372596 1713372626 6 ingress - 172.30.28.147 172.30.0.221 172.30.28.147 157.119.32.149 - - 9316 443 2 120 ACCEPT 2 OK
# PG-VPC(tgwatt out(SYN))
1713372600 1713372659 886200405623 ap-northeast-2 tgw-06003b9fdd132262e tgw-attach-03e8031df3b19fd46 egress tgw-attach-0ab4e132ce6b4f35e 6 IPv4 172.30.28.147 9316 157.119.32.149 443 apne2-az3 apne2-az3 886200405623 886200405623 vpc-013b0b4486425fd0a vpc-062f8dc77de6c6c92 subnet-0de5f8859f49abc49 subnet-08d2e606c177dae52 eni-094e13ec4ddfda270 eni-028c972ffb8c5c9da - - 2 120 0 0 0 0 2 OK
# NET-VPC(tgwatt in(SYN))
1713372600 1713372659 886200405623 ap-northeast-2 tgw-06003b9fdd132262e tgw-attach-0ab4e132ce6b4f35e ingress tgw-attach-03e8031df3b19fd46 6 IPv4 172.30.28.147 9316 157.119.32.149 443 apne2-az3 apne2-az3 886200405623 886200405623 vpc-013b0b4486425fd0a vpc-062f8dc77de6c6c92 subnet-0de5f8859f49abc49 subnet-08d2e606c177dae52 eni-094e13ec4ddfda270 eni-028c972ffb8c5c9da - - 2 120 0 0 0 0 2 OK
# NET-VPC(tgwatt out(SYN))
886200405623 ap-northeast-2 apne2-az3 vpc-062f8dc77de6c6c92 subnet-08d2e606c177dae52 - eni-028c972ffb8c5c9da 1713372612 1713372642 6 egress 1 172.31.9.247 157.119.32.149 172.30.28.147 157.119.32.149 - - 9316 443 2 120 ACCEPT 2 OK
# NET-VPC(nat in(SYN))
886200405623 ap-northeast-2 apne2-az3 vpc-062f8dc77de6c6c92 subnet-0c810e3b2cf788408 - eni-03daf08bc80321292 1713372648 1713372679 6 ingress - 172.30.28.147 172.31.9.36 172.30.28.147 157.119.32.149 - - 9316 443 2 120 ACCEPT 2 OK
```

transit gateway는 default route table을 갖는다. 이 route table이 default association, propagation route table이다. default association route table은 attachment를 생성하면 자동으로 연결되는 route table을 의미하며, default propagation route table은 attachment를 생성하면 자동으로 propagation이 추가되는 route table을 의미한다(propagation이 생성됨에 따라 dynamic route가 생성). 만약 transit gateway에 `Default route table association`, `Default route table propagation` 기능을 비활성화하면 aws는 default route table을 생성하지 않는다.

transit gateway route table에 blackhole route를 생성할 수 있다.

transit gateway에 vpc를 attach할 때 vpc에서 transit gateway로 route할 수 있도록 subnet route table에 transit gateway로의 route를 추가해야한다.

### Route propagation
vpc attachment의 경우 vpc의 cidr가 transit gateway route table에 propagation된다.

When dynamic routing is used with a VPN attachment or a Direct Connect gateway attachment, you can propagate the routes learned from the on-premises router through BGP to any of the transit gateway route tables.

When dynamic routing is used with a VPN attachment, the routes in the route table associated with the VPN attachment are advertised to the customer gateway through BGP.

For a Connect attachment, routes in the route table associated with the Connect attachment are advertised to the third-party virtual appliances, such as SD-WAN appliances, running in a VPC through BGP.

For a Direct Connect gateway attachment, allowed prefixes interactions control which routes are advertised to the customer network from AWS.

static route, propagated route가 동일 목적지를 갖는 경우 static route가 우선 순위를 갖기 때문에 propagated route는 route table에 포함되지 않는다.

### Routes for peering attachments

### Route evaluation order

### VPC and transit gateway route table differences