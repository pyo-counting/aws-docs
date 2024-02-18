**Use data plane functions for DNS failover and app recovery**
health check, Application Recovery Controller routing control을 포함하는 route 53 data plane 전세계에 분포되어 있으므로 심각한 상황에서도 100% 가용성을 보장하도록 설계했다. 세계에 분포된 data plane은 control plane에 의존하지 않는다. disaster recover 중 failover와 같은 시나리오의 경우 data plane 기능에 의존하는 route 53 health check, route 53 ARC route controle과 같은 기능을 사용해 DNS 업데이트 하는 것을 권장한다. 자세한 내용은 [Blog: Creating Disaster Recovery Mechanisms Using Amazon Route 53](https://aws.amazon.com/ko/blogs/networking-and-content-delivery/creating-disaster-recovery-mechanisms-using-amazon-route-53/)을 참고한다.

**Choosing TTL values for DNS records**
DNS resolver는 DNS TTL을 사용해 record를 캐싱한다. 모든 DNS record는 TTL 설정이 필요하다. 권장되는 값은 60 - 172,800초이다.

TTL 값의 크기에 따라 응답 latency와 신뢰성 간 trade-off가 있다. 짧은 TTL을 사용하면 DNS resolver는 더 자주 DNS 쿼리를 수행하기 때문에 record에 대한 업데이트 발생 시, 더 빨리 이러한 업데이트를 인지할 수 있다. 이는 비용 증가로 이어진다. 반대로 긴 TTL을 사용하면 캐싱 시간이 길어지면서 DNS 쿼리가 줄어들기 때문에 더 짧은 응답 latency를 제공할 수 있다. 올바른 값은 없으며 latency와 신뢰성 중요도에 따라 고려해야 한다.

추가적으로 고려할 사항은 다음과 같다.
- record에 대한 변경 사항이 적용될 때까지 충분히 기다릴 수 있는 시간을 TTL로 사용한다. 이는 delegation(NS record), 변경 사항이 거의 없는 MX record에 해당한다. 이러한 record의 경우 더 긴 TTL을 사용하는 것을 권장한다. 한 시간(3,600초) ~ 하루(86,400초) 사이의 값을 권장한다.
- 신속한 failover(특히 health check 대상 record)를 위해 낮은 TTL을 사용하는 것을 권장한다. 일반적으로 60 - 120초가 적당하다.
- 중요한 DNS record를 변경하는 경우 TTL을 일시적으로 줄이는 것을 권장한다. 그리고 reocrd를 변경하고 모니터링 할 수 있다. 그리고 쉽게 롤백할 수 있다. 만약 정상적으로 동작한다면 다시 TTL 값을 늘린다.

**CNAME records**
DNS CNAME record는 1개의 도메인을 사용해 다른 도메인을 가리키는 방법이다. DNS resolver가 domain-1.example.com 도메인을 쿼리한 결과로 domain-2.example.com을 가리키는 CNAME record임을 알게되는 경우 응답하기 전에 domain-2.example.com에 대해 다시 DNS 쿼리해야 한다.

이는 DNS resolver가 더 쿼리, latency 증가로 이어진다. 대안으로 alias record를 사용하는 것을 권장한다. route 53가 제공하는 alias record는 aws resource, 동일 hosted zone에 존재하는 다른 domain에 대해 직접 응답할 수 있도록한다.

**Advanced DNS routing**
- When using geolocation, geoproximity, or latency-based routing, always set a default, unless you want some clients to receive no answer responses.
- To minimize application latency, use latency-based routing. This type of routing data can change frequently.
- To provide routing stability and predictability, use either geolocation or geoproximity routing.

**DNS change propagation**
record 또는 hosted zone을 생성하거나 업데이트하는 경우 인터넷을 통해 전체에 변경 사항이 반영되는 데 시간이 소요된다. 이를 cahnge propagation이라고 부른다. 일반적으로 이 시간은 1분 미만이지만 한 위치에서의 동기화 문제로 인한 지연, control plane에서의 문제로인한 지연이 발생할 수도 있다. change propagation이 수행됐음을 확실하게 확인해야 하는 경우 GetChange API를 사용해 변경 사항이 적용됐는지(Status =INSYNC) 확인할 수 있다.

**DNS delegation**
DNS 내에서 subdomain level에 따라 위임(delegation)을 수행하는 경우 항상 바로 위 level 도메인에서 위임하는 것을 권장한다. 예를 들어 www.dept.example.com를 위임하는 경우 example.com zone이 아닌 dept.example.com zone에서 위임을 수행해야 한다. Delegations from a grandparent to a child zone might not work at all or work only inconsistently.

**Size of DNS response**
용량이 큰 응답을 생성하는 것을 피해야 한다. 응답이 512 byte를 초과하면 DNS resolver는 UDP 대신 TCP를 통해 재시도를 수행해야 하므로 신뢰성이 떨어지고 latency가 커질 수 있다. 이를 위해 multivalue routing을 사용하는 것을 권장한다. multivalue는 512 바이트 경계를 넘지않기 위해 최대 8개의 ip를 응답한다.