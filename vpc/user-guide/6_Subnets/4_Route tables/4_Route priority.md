일반적으로 트래픽을 라우팅할 때 가장 구체적인 경로를 사용한다. 이를 longest prefix match라고 부른다. 만약 route table에 중첩되는 경로가 있을 때는 추가 규칙이 적용된다.

아래는 route priority 순서다.
1. Longest prefix (for example, 10.10.2.15/32 has priority over 10.10.2.0/24)
2. Static routes (like VPC peering and internet gateway connections)
3. Prefix list routes
4. Propagated routes
    1. Direct Connect BGP routes (dynamic routes)
    2. VPN static routes
    3. VPN BGP routes (dynamic routes) (like virtual private gateways)