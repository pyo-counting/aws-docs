NAT device를 사용해 private subnet 내 인스턴스를 인터넷, 다른 vpc, on-premise 네트워크에 연결할 수 있다. 인스턴스는 vpc 밖의 서비스와 통신할 수 있지만 외부 연결 요청은 받을 수 없다.

NAT device는 source의 ipv4 주소를 NAT device의 주소로 대체한다. 반대로 NAT device가 인스턴스에 트래픽을 응답할 때, NAT device는 원래 ipv4로 변환한다.

Consideration
- NAT device는 실제로는 NAT, PAT을 수행한다.