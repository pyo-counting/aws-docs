공격자는 DNS 쿼리를 가로채고 해당 엔드포인트의 실제 IP 주소 대신 자신의 IP 주소를 DNS resolver에게 응답함으로써 웹 서버와 같은 인터넷 엔드포인트로의 트래픽을 하이재킹하기도 합니다. 그리고 사용자는 응답된 IP 주소에 접속하게 된다.

DNS 트래픽을 보호하기 위한 프로토콜인 DNSSEC(Domain Name System Security Extensions)를 구성하여 DNS spoofing 또는 man-in-the-middle attack을 차단할 수 있다.