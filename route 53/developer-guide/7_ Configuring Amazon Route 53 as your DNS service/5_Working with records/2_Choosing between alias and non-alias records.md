alias record는 route 53에서만 제공하는 추가 기능이다. alias reocrd는 cf distribution, s3 bucket과 같은 AWS resource로 트래픽을 라우팅할 수 있다.

CNAME record와 다른점은 zone apex(DNS namespace)에 대해 alias reocrd를 생성할 수 있다. 예를 들어 example.com alias record를 사용해 www.example.com(C record type이 아니라면)로 라우팅할 수 있다.

**NOTE**
> zone apex는 naked domain이라고도 부른다. 이름 그대로 sub-domain이 붙지 않는 상태를 말한다.

route 53이 alias record에 대해 DNS 쿼리를 요청 받을 때 AWS resource에 맞는 값을 사용 및 반환한다.
- `An Amazon API Gateway custom regional API or edge-optimized API`: Route 53 responds with one or more IP addresses for your API.
- `An Amazon VPC interface endpoint`: Route 53 responds with one or more IP addresses for your interface endpoint.
- `A CloudFront distribution`: Route 53 responds with one or more IP addresses for CloudFront edge servers that can serve your content.
- `An Elastic Beanstalk environment`: Route 53 responds with one or more IP addresses for the environment.
- `An Elastic Load Balancing load balancer`: Route 53 responds with one or more IP addresses for the load balancer. This includes Application Load Balancer, Classic Load Balancer and, Network Load Balancer.
- `An AWS Global Accelerator accelerator`: Route 53 responds with the IP addresses for the accelerator.
- `An Amazon S3 bucket that is configured as a static website`: Route 53 responds with one IP address for the Amazon S3 bucket.
- `Another Route 53 record of the same type in the same hosted zone`: Route 53 responds as if the query is for the record that is referenced by the alias record (see Comparison of alias and CNAME records).
- `AWS AppSync domain name`: Route 53 responds with one or more IP addresses for your interface endpoint.

## [Comparison of alias and CNAME records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html#resource-record-sets-choosing-alias-non-alias-comparison)
alias record는 CNAME record와 유사하지만 몇 가지 다른 점이 존재한다.
- Resources that you can redirect queries to
    - alias record: s3 bucket, cf distribution, 동일 hosted zone의 다른 record
    - CNAME record: 다른 모든 DNS record로 리다이렉트가 가능하다.
- Creating records that have the same name as the domain (records at the zone apex)
    - alias record: zone apex(DNS namespace)에 대해 alias reocrd를 생성할 수 있다(alias record를 CNAME record로 리다이렉트하는 것은 불가). alias record 타입은 라우팅할 record와 동일해야 한다.
    - CNAME record: hosted zone 이름과 동일한 이름을 갖는 CNAME record를 만들 수 없다.
- Pricing for DNS queries
    - alias record: AWS resource에 대한 alias 쿼리는 비용이 들지 않는다.
    - CNAME record: 비용이 든다.
- Record type specified in the DNS query
    - alias record: alias record의 이름과 타입이 DNS 쿼리 요청과 일치하는 경우에만 응답한다.
    - CNAME record: DNS 쿼리 요청에 명시된 record 타입과 관계 없이 리다이렉트한다.
- How records are listed in dig or nslookup queries
    - alias record: alias record 생성 시 선택한 record 타입이 표기된다.
    - CNAME record: CNAME record로 표기된다.