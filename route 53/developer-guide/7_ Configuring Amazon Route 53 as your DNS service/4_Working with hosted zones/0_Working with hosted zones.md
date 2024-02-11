hosted zone은 reocrd에 대한 container 역할을 한다. record는 특정 도메인, subdomain에 대해 트래픽을 어떻게 라우팅할지에 대한 정보를 포함한다. hosted zone과 관련 도메인은 동일한 이름을 갖는다. hosted zone은 두 가지 타입이 있다:

- public hosted zone: 인터넷 트래픽에 대한 라우팅을 위한 record를 포함한다.
- private hosted zone: AWS vpc 트래픽에 대한 라우팅을 위한 record를 포함한다.

A hosted zone is a container for records, and records contain information about how you want to route traffic for a specific domain, such as example.com, and its subdomains (acme.example.com, zenith.example.com). A hosted zone and the corresponding domain have the same name. There are two types of hosted zones:

Public hosted zones contain records that specify how you want to route traffic on the internet. For more information, see Working with public hosted zones.

Private hosted zones contain records that specify how you want to route traffic in an Amazon VPC. For more information, see Working with private hosted zones.