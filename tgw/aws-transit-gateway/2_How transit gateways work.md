transit gateway는 regional virtual router로 동작한다. transit gateway를 통한 라우팅은 3 계층에서 이루어지며 packet은 next-hop attachement로 보내진다.

### Equal Cost Multipath routing

## Availability Zones
vpc를 transit gateway에 attach할 때, transit gateway가 vpc로 트래픽을 라우팅할 수 있도록 1개 이상의 az를 활성화 해야한다. az 활성화는 각 az에 대해 1개의 subnet을 명시하면 된다. transit gateway는 명시한 각 subnet의 ip 주소 1개를 사용하는 network interface(type: transit-gateway)를 생성한다. az를 활성화하면 트래픽은 vpc 내 모든 subnet으로 라우팅될 수 있다. 하지만 해당 az에 존재하는 aws resource만 transit gateway에 도달할 수 있다.

만약 목적지 attachment가 존재하지 않는 az에서 트래픽이 유입된 경우 transit gateway는 내부적으로 attachment가 존재하는 랜덤 az로 트래픽을 라우팅한다. 이러한 타입의 cross az 트래픽은 추가 과금이 없다.

**Using appliance mode support**

If you plan to configure a stateful network appliance in your VPC, you can enable appliance mode support for the VPC attachment in which the appliance is located. This ensures that the transit gateway uses the same Availability Zone for that VPC attachment for the lifetime of a flow of traffic between source and destination. It also allows the transit gateway to send traffic to any Availability Zone in the VPC, as long as there is a subnet association in that zone. For more information, see Example: Appliance in a shared services VPC.

## Routing
vpc의 subnet route table과 transit gateway의 route table에 대해 잘 이해해야 한다. subnet route table에서는 target으로 transit gateway로의 route를 설정할 수 있다. 트래픽은 transit gateway attachment의 network interface를 통해 transit gateway로 전송된다. 그 다음 transit gateway에서는 해당 transit gateway attachement의 transit gateway route table을 확인해 다음 hop(transit gateway attachment)을 결정한다.

transit gateway route table은 target으로 transit gateway attachament로의 route만 설정할 수 있다. transit gateway route table을 통해 transit gateway attachment로 라우팅이 이루어진다. transit gateway attachment는 실제 특정 az 내 subnet의 network interface이기 때문에 해당 subnet route table을 이용한다.