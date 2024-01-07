## [Domain Name System (DNS) concepts](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html#route-53-concepts-domain-name-system-dns)
- `alias record`: route 53에서만 제공하는 레코드 타입으로 cloudfront distribution, s3에 대해 트래픽을 라우팅하기 위해 사용한다.
- `authoritative name server`: A name server that has definitive information about one part of the Domain Name System (DNS) and that responds to requests from a DNS resolver by returning the applicable information. For example, an authoritative name server for the .com top-level domain (TLD) knows the names of the name servers for every registered .com domain. When a .com authoritative name server receives a request from a DNS resolver for example.com, it responds with the names of the name servers for the DNS service for the example.com domain.

    Route 53 name servers are the authoritative name servers for every domain that uses Route 53 as the DNS service. The name servers know how you want to route traffic for your domain and subdomains based on the records that you created in the hosted zone for the domain. (Route 53 name servers store the hosted zones for the domains that use Route 53 as the DNS service.)

    For example, if a Route 53 name server receives a request for www.example.com, it finds that record and returns the IP address, such as 192.0.2.33, that is specified in the record.
- `DNS resolver`: A DNS server, often managed by an internet service provider (ISP), that acts as an intermediary between user requests and DNS name servers. When you open a browser and enter a domain name in the address bar, your query goes first to a DNS resolver. The resolver communicates with DNS name servers to get the IP address for the corresponding resource, such as a web server. A DNS resolver is also known as a recursive name server because it sends requests to a sequence of authoritative DNS name servers until it gets the response (typically an IP address) that it returns to a user's device, for example, a web browser on a laptop computer.
- `hosted zone`: 도메인에 대한 트래픽을 라우팅 정보를 포함하는 container.
- `reusable delegation set`: 
- `routing policy`:
- `time to live (TTL)`: