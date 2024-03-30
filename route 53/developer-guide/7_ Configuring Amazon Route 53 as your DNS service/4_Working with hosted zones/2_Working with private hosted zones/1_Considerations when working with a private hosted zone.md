
**Amazon VPC settings**  
private hosted zone을 사용하기 위해 아래 VPC 설정을 사용해야 한다.
- enableDnsHostnames
- enableDnsSupport

**Route 53 health checks**  
private hosted zone은 failover, multivalue answer, weighted, latency, geolocation records route 53 health check를 사용할 수 있다.

**Supported routing policies for records in a private hosted zone**  
private hosted zone은 아래 routing policy를 사용할 수 있다.
- Simple routing
- Failover routing
- Multivalue answer routing
- Weighted routing
- Latency-based routing
- Geolocation routing

**Split-view DNS**  
route 53을 사용해 split-view DNS(또는 split-horizon DNS)을 구성할 수 있다. split-view DNS를 사용하면 동일 도메인 이름에 대해 내부 / 외부 사용을 할 수 있다. 
1. 동일한 이름을 갖는 public, private hosted zone을 생성한다. 
2. private hosted zone에 대해 VPC를 연결한다. 해당 VPC의 route 53 resolver는 private hosted zone을 사용한다.
3. 각 hosted zone에 record를 생성한다.

**Public and private hosted zones that have overlapping namespaces**  
부분적으로 중복되는 private, public hosted zone이 있을 경우 resolver는 더 정확한 매칭을 사용한다.
1. resolver는 dns 쿼리에 매칭되는 private hosted zone이 있는지 확인한다. 확인 시 매칭 방법은 다음과 같다.
    - DNS 쿼리 요청 값과 동일한 이름을 갖는 private hosted zone
    - DNS 쿼리 요청 값과 더 정확항 매칭이 있는 private hosted zone
    만약 매칭되는 private hosted zone이 없을 경우 resolver는 쿼리를 public DNS resolver로 포워딩하게되며 이는 일반적인 DNS 쿼리와 동일하게 처리된다.
2. private hosted zone에 매칭되는 경우 해당 private hosted zone 내에서 record를 찾아 값을 반환한다.   
    **NOTE**
    > 매칭하는 private hosted zone은 있지만 매칭되는 record는 없는 경우 resolver는 public DNS resolver로 포워딩하지 않고 NXDOMAIN을 반환한다.

**Private hosted zones that have overlapping namespaces**  
부분적으로 중복되는 두 개의 private hosted zone이 있을 경우 resolver는 더 정확한 매칭을 사용한다.
1. resolver는 dns 쿼리 값과 정확하게 일치하는 private hosted zone 이름이 있는지 확인한다.
2. 정확하게 일치하는 private hosted zone이 없을 경우 DNS 쿼리 요청 값과 더 정확항 매칭이 있는 private hosted zone을 확인한다.
3. private hosted zone에서 쿼리 요청에 대한 record를 찾는다. record가 없는 경우 NXDOMAIN을 반환한다.

**NOTE**
> If you have a private hosted zone (example.com) and a Route 53 Resolver rule that routes traffic to your network for the same domain name, the Resolver rule takes precedence.

**Private hosted zones and Route 53 Resolver rules**  

**Delegating responsibility for a subdomain**  
private hosted zone에서는 subdomain에 대한 책임을 위임하기 위해 NS 레코드를 생성할 수 없다.

**Custom DNS servers**  
EC2를 사용해 DNS server를 직접 생성 및 관리하는 경우 Amazon-provided DNS server로 쿼리를 라우팅할 수 있도록 설정해야 한다. 주소는 VPC 네트워크 대역 + 2이다. 예를 들어 VPC의 CIDR가 10.0.0.0/16일 경우 DNS 서버의 주소는 10.0.0.2이다.

**Required IAM permissions**  