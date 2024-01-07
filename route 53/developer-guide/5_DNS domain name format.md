## [Using an asterisk (*) in the names of hosted zones and records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/DomainNameFormat.html#domain-name-format-asterisk)

- Hosted zones
    - domain 가장 왼쪽 label에 *를 포함할 수 없다. 예를 들어 *.example.com는 허용되지 않는다.
    - 다른 위치에 *를 포함하면 DNS는 와일드카드가 아닌 ASCII 문자로 인식한다.

- Records: *의 위치에 따라 와일드카드 또는 ASCII 문자로 인식한다.
    - \*는 가장 왼쪽 label에 위치해야 하며 다른 문자와 같이 사용하면 안된다. 예를 들어 prod.*.example.com는 DNS가 와일드카드가 아닌 ASCII 문자로 인식한다. 그리고 *prod.example.com 와 같이 사용할 수 없다.
    - 명시된 도메인이 우선순위를 갖는다. 예를 들어 *.example.com, acme.example.com가 있을 때 acme.example.com에 대한 DNS 쿼리는 acme.example.com record의 값을 응답한다.
    - *는 해당 도메인과 모든 서브 도메인에 적용된다. 예를 들어 *.example.com에 대해 zenith.example.com, acme.zenith.example.com에 대한 DNS 쿼리는 *.example.com record의 값을 응답한다.
    - You can configure Route 53 to return the same response to DNS queries both for all subdomains at the same level and for the domain name. For example, you can configure Route 53 to respond to DNS queries such as acme.example.com and zenith.example.com using the example.com record. Perform the following steps:
        1. Create a record for the domain, such as example.com.
        2. Create an alias record for the subdomain, such as *.example.com. Specify the record that you created in step 1 as the target for the alias record.
    - NS record 타입에 대해 *를 와일드카드로 사용할 수 없다.