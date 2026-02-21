resolver는 기본적으로 아래 도메인에 대한 쿼리가 기본적으로 resolve 되도록하는 autodefined system rules을 자동 생성한다.
- private hosted zone, ec2 전용 도메인(`compute.amazonaws.com`, `compute.internal` 등): 사용자가 `.`, `com`과 같이 덜 구체적인 도메인에 대해 conditional forwarding rules을 생성하더라도 정상적으로 resolve 되도록 보장한다.
- 퍼블릭 예약 도메인 이름(`localhost`, `10.in-addr.arpa` 등): DNS best practice는 public DNS 서버로 쿼리를 전달하는 대신 로컬에서 응답하는 것을 권장한다. 관련해서 [RFC 6303, Locally Served DNS Zones](https://datatracker.ietf.org/doc/html/rfc6303)을 참고한다.

> **Note**:  
> `.`, `com`에 onditional forwarding rules을 생성하는 경우, `amazonaws.com`에 대한 system rule도 함께 생성하는 것을 권장한다. (system rule은 VPC Resolver가 특정 도메인 및 하위 도메인에 대한 DNS 쿼리를 로컬에서 확인하도록 함) 이 system rule을 생성하면 성능이 향상되고, 네트워크로 전달되는 쿼리 수가 줄어들며, VPC Resolver 비용이 절감된다.

autodefined rule을 재정의(override)하려면, 동일한 도메인 이름에 대해 conditional forwarding rule을 생성하면 된다.

일부 autodefined rules을 비활성화할 수도 있다. 자세한 내용은 [Forwarding rules for reverse DNS queries in VPC Resolver](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-rules-managing.html#resolver-automatic-forwarding-rules-reverse-dns)을 참고한다.

VPC Resolver는 아래 autodefined rule을 생성한다.

Rules for private hosted zones  
VPC에 연결하는 각 private hosted zone에 대해 VPC Resolver는 rule을 생성하고 이를 해당 VPC에 연결한다. private hosted zone을 여러 VPC에 연결하면 VPC Resolver는 동일 규칙을 동일한 VPC들에 연결한다.

이 rule의 유형은 Forward다.

Rules for various AWS internal domain names  
이 섹션의 내부 도메인 이름에 대한 모든 rule 유형은 Forward다. VPC Resolver는 이러한 도메인 이름에 대한 DNS 쿼리를 해당 VPC의 authoritative name servers로 전달한다.

> **Note**:  
> VPC Resolver는 VPC의 enableDnsHostnames flag를 true로 설정할 때 이러한 rule의 대부분을 생성한다. VPC Resolver는 사용자가 Resolver endpoint를 사용하지 않더라도 규칙을 생성한다.

VPC Resolver는 VPC의 enableDnsHostnames flag를 true로 설정할 때 다음 autodefined rule을 생성하여 VPC에 연결한다.
- `${Region-name}.compute.internal`: us-east-1 region은 이 도메인 이름을 사용하지 않는다(예: eu-west-1.compute.internal).
- `${Region-name}.compute.amazon-domain-name`: us-east-1 region은 이 도메인 이름을 사용하지 않는다(예: `eu-west-1.compute.amazonaws.com`, `cn-north-1.compute.amazonaws.com.cn`).
- `ec2.internal`. us-east-1 region만 이 도메인 이름을 사용한다.
- `compute-1.internal`: us-east-1 region만 이 도메인 이름을 사용한다.
- `compute-1.amazonaws.com`: us-east-1 region만 이 도메인 이름을 사용한다.

아래 autodefined rule은 VPC의 enableDnsHostnames flag를 true로 설정할 때 VPC Resolver가 생성하는 rule에 대한 reverse DNS lookup 용도다.
- `10.in-addr.arpa`
- `16.172.in-addr.arpa` ~ `31.172.in-addr.arpa`
- `168.192.in-addr.arpa`
- `254.169.254.169.in-addr.arpa`
- VPC의 각 CIDR 대역에 대한 rule. 예를 들어, VPC가 10.0.0.0/23의 CIDR 범위를 갖는 경우 VPC Resolver는 다음 rule을 생성한다.
    - `0.0.10.in-addr.arpa`
    - `1.0.10.in-addr.arpa`

VPC의 enableDnsHostnames flag를 true로 설정하면 다음과 같은 localhost 관련 도메인에 대한 autodefined rule도 생성되어 VPC에 연결된다.
- `localhost`
- `localdomain`
- `127.in-addr.arpa`
- `1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa`
- `0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa`

tgw 또는 VPC perring을 통해 VPC를 다른 VPC와 연결하고 DNS support가 활성화된 경우, VPC Resolver는 다음 autodefined rule을 생성하여 VPC에 연결한다.
- peer VPC의 IP 주소 범위에 대한 역방향 DNS 조회 (예: `0.192.in-addr.arpa`)
- VPC에 IPv4 CIDR 블록을 추가하면 VPC Resolver는 새 IP 주소 범위에 대한 autodefined rule을 추가한다.
- 다른 VPC가 다른 region에 있는 경우
    - `${Region-name}.compute.internal`: us-east-1 region은 이 도메인 이름을 사용하지 않는다.
    - `${Region-name}.compute.amazon-domain-name`: us-east-1 region은 이 도메인 이름을 사용하지 않는다.
    - `ec2.internal`: us-east-1 region만 이 도메인 이름을 사용한다.
    - `compute-1.amazonaws.com`: us-east-1 region만 이 도메인 이름을 사용한다.

A rule for all other domains
VPC Resolver는 모든 도메인 이름에 적용되는 .(점) rule을 생성한다. 이 . 규칙은 Recursive 타입을 가지며, 이는 해당 rule이 VPC Resolver를 recursive resolver 작동하게 만든다는 것을 의미한다. 해당 rule은 AWS console을 통해 확인 가능하다.