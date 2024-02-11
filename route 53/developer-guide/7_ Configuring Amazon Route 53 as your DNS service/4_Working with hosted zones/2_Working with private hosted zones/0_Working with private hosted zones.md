private hosted zone 생성 시 아래 name server가 사용된다.
- ns-0.awsdns-00.com
- ns-512.awsdns-00.net
- ns-1024.awsdns-00.org
- ns-1536.awsdns-00.co.uk

위 name server는 예약되었으며 route 53 public hosted zone에 사용되지 않는다.

인터넷에서 위 name server를 확인할 수 있지만 route 53 resolver는 name server 주소에 연결하지 않는다. 그리고 쿼리를 수행하더라도 private hosted zone의 정보가 반환되지 않는다. 