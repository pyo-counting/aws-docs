## [Domain Name System (DNS) concepts](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html#route-53-concepts-domain-name-system-dns)
- `alias record`: route 53에서만 제공하는 레코드 타입으로 cloudfront distribution, s3에 대해 트래픽을 라우팅하기 위해 사용한다.
- `authoritative name server`: DNS의 한 부분에 대한 확실한 정보를 가지고 있으며 DNS resolver 요청에 대해 해당 정보를 응답하는 name server. 예를 들어 .com TLD에 대한 authoritative name server는 등록된 모든 .com 도메인에 대한 name server의 이름을 알고 있다. .com TLD에 대한 authoritative name serve는 example.com 에 대한 DNS resolver의 질의를 받으면 example.com 도메인에 대한 DNS 서비스에 대한 name server의 이름을 응답한다.

    route 53은 authoritative name server다. name server는 hosted zone에 만든 record을 기반으로 도메인, 서브-도메인의 트래픽을 라우팅하는 방법을 알고 있다. (route 53 name server는 route 53을 DNS 서비스로 사용하는 도메인의 호스트 영역을 저장한다.)

    예를 들어, Route 53 name server가 www.example.com 에 대한 요청을 수신할 경우, record를 찾은 다음 192.0.2.33과 같은 IP 주소를 반환한다.
- `DNS resolver`: DNS resolver는 일반적으로 ISP에서 관리하며 사용자와 DNS name server의 중개자 역할을 수행한다. DNS resolver는 ip 주소와 같은 응답을 받기 위해 재귀적으로 authoritative DNS name server에 요청을 보내기 때문에 recursive name server라고도 한다.
- `hosted zone`: 도메인, 서브-도메인에 대한 트래픽을 라우팅 정보를 포함하는 container. hosted zone의 이름은 도메인 이름과 동일하다.
- `reusable delegation set`: 여러 hosted zone에서 사용할 수 있는 4개의 authoritative name server 집합. 기본적으로 route 53은 새로운 hosted zone마다 임의의 authoritative name server을 할당한다. 많은 수의 도메인에 대해 DNS route 53으로 마이그레이션을 쉽게하기 위해서 reusable delegation set을 생성한 후 hosted zone에 연결할 수 있다(이미 존재하는 hosted zone에 대해서는 불가).
- `routing policy`: record에 대한 설정으로 route 53이 DNS 쿼리에 대한 응답을 하는 방법에 대해 설정한다. 아래 옵션을 지원한다.
    - `Simple routing policy`: 사용자가 지정한 resource로 트래픽을 라우팅한다. 동일한 타입, 이름을 갖는 record를 여러개 만들 수 없다. 대신 동일 record에 여러 값을 명시할 수 있다(alias reocrd는 1개의 AWS resource만 명시할 수 있다). 이 때 route 53은 랜덤 순서로 resolver에게 응답을 한다. 그리고 이러한 값은 health check가 되지 않은 값이며 단지 사용자가 명시한 값일 뿐이다.
    - `Failover routing policy`: active-passive failover를 위해 사용한다. health check를 통해 healthy한 resource로 트래픽을 라우팅한다.
    - `Geolocation routing policy`: 사용자 위치에 따라 인터넷 트래픽을 다른 리소스로 라우팅한다. 예를 들어 유럽에서 발생한 DNS 쿼리는 Frankfurt region의 ELB로 트래픽을 라우팅할 수 있다.
    - `Geoproximity routing policy:` 리소스의 위치를 기준으로 트래픽을 라우팅하고 선택적으로 한 위치에 있는 리소스에서 다른 위치에 있는 리소스로 트래픽을 이동하려는 경우에 사용
    - `Latency routing policy`: 여러 위치에 리소스를 운영하고 가장 짧은 지연을 제공할 수 있는 리소스로 트래픽을 라우팅한다.
    - `IP-based routing policy`: 사용자 위치를 기준으로 트래픽을 라우팅하고 트래픽이 발생하는 IP 주소를 가질 때 사용한다.
    - `Multivalue answer routing policy`: Route 53에서 임의로 최대 8개의 정상 record를 선택하여 DNS 쿼리에 응답한다. route 53이 각 resource에 대해 health check를 수행하고 healthy한 resoures의 집합(최대 8개)에 대해서만 응답한다.
    - `Weighted routing policy`: 사용자가 할당한 비중으로 트래픽을 라우팅한다.
- `time to live (TTL)`: DNS resolver가 해당 record의 값을 캐시할 시간을 설정. TTL이 만료되기 전에 DNS resolver가 동일한 도메인에 대한 쿼리를 요청 받으면 캐시된 값을 응답한다.

    TTL이 클수록 route 53에 대한 쿼리가 적어지기 때문에 요금이 절약된다. 하지만 반대로 레코드의 값의 변경에 따른 전파가 느려진다는 단점도 있다.

## [Control and data plane concepts](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html#route-53-concepts-control-and-data-plane)
route 53는 다른 AWS managed service와 마찬가지로 control plane, data plane 아키텍처로 분리해서 제공한다. control plane은 데이터 일관성에 최적화된 반면 data plane은 가용성에 최적화되었다. data plane의 탄력적인 설계를 통해 control plane은 사용할 수 없는 상황에도 가용성을 제공한다.

route 53 public, private dns, health check의 경우 control plane은 us-east-1 AWS region에 위치하며 data plane은 전 세계에 분산되어있다.

route 53은 아래와 같이 control plane, data plane을 나눠 제공한다.
- route 53 public, private DNS의 경우 control plane은 DNS entry를 관리할 수 있는 콘솔과 API다. data plane은 200개 이상의 PoP(Points of Presence)에서 hosted zone과 health check 데이터 기반으로 DNS 쿼리에 응답하는 authoritative DNS 서비스다,
- route 53 health check의 경우 control plane은 health check을 생성, 업데이트, 삭제할 수 있는 콘솔과 API다. data plane은 글로벌 분산 서비스로 health check을 수행하고 결과를 집계하여 route 53 public, private DNS와 AWS Global Accelerator의 data plane으로 전달된다.
- route 53 resolver의 control plane은 vpc 설정, resolver 규칙, 쿼리 로깅 정책, DNS 방화벽 정책을 관리할 수 있는 콘솔과 API다. data plane은 VPC에서 DNS 쿼리를 처리하는 DNS resolver, 다른 resolver로 전달하는 endpoint, 필터링하는 정책을 적용하는 firewall이다. resolver는 regional 서비스로 각 AWS region에서 control plane과 data plane이 독립적으로 구동된다.
- route 53 도메인 등록은 us-east-1 aws region control plane에서만 관리된다.