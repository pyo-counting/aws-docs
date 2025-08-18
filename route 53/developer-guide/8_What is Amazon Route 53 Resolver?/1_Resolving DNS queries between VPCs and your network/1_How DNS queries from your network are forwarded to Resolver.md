Steps for creating default inbound endpoints

Here's how Resolver resolves DNS queries that originate on your network via a default inbound endpoint:
1. 사용자 네트워크의 웹 브라우저 또는 애플리케이션이 resolver에게 DNS 쿼리를 수행한다.
2. 사용자 네트워크의 resolver는 내부 규칙에 따라 Route 53 inbound endpoint에게 DNS 쿼리를 forwarding한다.
3. inbound endpoint는 route 53 resolver에게 쿼리를 forwarding한다.
4. route53 resolver는 내부 또는 recursive lookup을 수행해 public DNS 서버에서 DNS 쿼리에 대한 응답을 얻는다.
5. route 53 resolver는 inbound endpoint에게 응답을 전달한다.
6. inbound endpoint는 사용자 네트워크로 응답을 전달한다.
7. 사용자 네트워크의 resolver는 애플리케이션에게 응답을 전달한다.
8. 애플리케이션은 DNS 쿼리 응답 IP를 사용해 통신을 수행한다.

Steps for creating delegation inbound endpoints

Here's how Resolver resolves DNS queries that originate on your network via a delegation inbound endpoint:
1. on-premises에서 private hosted zone으로 subdomain을 위임해야 한다. inbound delegation endpoint를 통해 subdomain을 위임하는 것이므로, 위임되는 subdomain의 glue record 값에 inbound endpoint의 IP 주소를 사용한다.  
    **Note**:  
    DNS 쿼리가 resolving 가능하기 위해서 glue record를 등록이 필요할 수도 있다. 부모 domain과 동일한 zone 내에 있는 name server로 subdomain을 위임했을 경우 glue record가 필요하다.
2. A web browser or another application on your network submits a DNS query for a domain name that you delegated to the Route 53 Resolver.
3. A resolver on your network forwards the query to the IP addresses in your inbound endpoint.
4. The inbound endpoint delegates the query to Resolver.
5. Resolver returns the address to the AWS resource from the private hosted zone to the inbound endpoint.
6. The inbound endpoint returns the value to the resolver on your network.
7. The resolver on your network returns the value to the application.
8. Using the value that was returned by Resolver, the application submits a request, for example, a request for an object in an Amazon S3 bucket.

Creating an inbound endpoint doesn't change the behavior of Resolver, it just provides a path from a location outside the AWS network to Resolver.