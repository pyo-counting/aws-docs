transit gateway는 regional virtual router로 동작한다. transit gateway를 통한 라우팅은 3 계층에서 이루어지며 packet은 next-hop attachement로 보내진다.

### Equal Cost Multipath routing

## Availability Zones
vpc를 transit gateway에 attach할 때 transit gateway가 vpc로 트래픽을 라우팅할 수 있도록 1개 이상의 az를 활성화 해야한다. az 활성화는 각 az에 대해 1개의 subnet을 명시하면 된다. transit gateway는 명시한 각 subnet의 ip 주소 1개를 사용하는 network interface(type: transit-gateway)를 생성한다. az를 활성화하면(transitgateway attachment 생성) transit gateway는 transit gateway attachment를 통해 해당 subnet, az뿐만 아니라 전체 vpc로 트래픽을 라우팅할 수 있다. 하지만 반대로 해당 transitgateway attachment가 있는 az의 aws resource만 transit gateway에 접근할 수 있다.

기본적으로 transitgateway는 트래픽이 인입되는 transitgateway attachment의 az와 동일한 az의 transitgateway attachment로 traffic을 보낸다. 하지만 목적지 attachment가 존재하지 않는 az에서 트래픽이 유입된 경우 transit gateway는 내부적으로 attachment가 존재하는 랜덤 az로 트래픽을 라우팅한다. 이러한 타입의 cross az 트래픽은 추가 과금이 없다.

**Using appliance mode support**
vpc에서 stateful network appliance 사용을 하는 경우 해당 vpc를 attach할 때 appliance mode를 활성화할 수 있다. 이를 통해 transit gateway는 동일 트래픽 in, out에 대해 해당 vpc의 동일 az로 트래픽을 라우팅한다. 이는 해당 vpc attachment에 대해서만 활성화되는 것이다. 동일 트래픽에 대해 다른 vpc attachment로 보낼 때는 다른 az로 트래픽을 라우팅할 수 있다.

## Routing
vpc의 subnet route table과 transit gateway의 route table에 대해 잘 이해해야 한다. subnet route table에서는 target으로 transit gateway로의 route를 설정할 수 있다. 트래픽은 transit gateway attachment의 network interface 중 동일한 az에 있는 network interface를 통해 transit gateway로 전송된다. 그 다음 transit gateway에서는 해당 transit gateway attachement의 transit gateway route table을 확인해 다음 hop(transit gateway attachment)을 결정한다.

transit gateway route table은 target으로 transit gateway attachament로의 route만 설정할 수 있다. transit gateway route table을 통해 transit gateway attachment로 라우팅이 이루어진다. transit gateway attachment로의 라우팅은 실제 network interface로의 라우팅이다. network interface는 다음 hop을 결정하기 위해 subnet route table을 이용한다.

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