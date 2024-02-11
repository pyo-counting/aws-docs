각 public hosted zone에 대해 route 53은 NS, SOA record를 생성한다.

## [The name server (NS) record](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/SOA-NSrecords.html#NSrecords)
route 53은 hosted zone과 동일한 이름을 갖는 NS record를 자동으로 생성한다. NS record는 hosted zone의 4개 authoritative name server를 값으로 갖는다.

아래는 route 53 name server 예시다.
- ns-2048.awsdns-64.com
- ns-2049.awsdns-65.net
- ns-2050.awsdns-66.org
- ns-2051.awsdns-67.co.uk

## [The start of authority (SOA) record](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/SOA-NSrecords.html#SOArecords)
SOA record는 도메인에 대한 기본 DNS 정보를 값으로 갖는다.
```
ns-2048.awsdns-64.net. hostmaster.example.com. 1 7200 900 1209600 86400
```

아래는 각 요소 별 설명이다.
- `ns-2048.awsdns-64.net.`: SOA record를 생성한 name server
- `hostmaster.example.com.`: 관리자의 이메일 주소. `@` 문자는 `.`으로 대체된다. 
- `1`: record 수정 시 선택적으로 증가시킬 수 있다. AWS는 자동으로 값을 증가시키지 않는다.
- `7200`: secondary DNS 서버가 primary DNS에 SOA record에 대한 업데이트가 있었는지 쿼리하는 주기(s)
- `900`: secondary DNS 서버가 zone transefer 실패 시 재시도를 위해 기다리는 시간.
- `1209600`: secondary DNS 서버가 zone transter 실패 시 재시도를 수행하는 시간. zone transfer가 이 시간을 경과하면 secondary server는 자신의 데이터가 오래되었다고 생각하고 쿼리에 대한 응답을 하지 않는다.
- `TTL 시간`: route 53이 아래 응답을 할 경우 recursive resolver가 캐시해야 할 시간.
    - NXDOMAIN: There is no record of any type with the name that is specified in the DNS query, such as example.com. There also are no records that are children of the name that is specified in the DNS query, such as zenith.example.com.
    NODATA: There is at least one record with the name that is specified in the DNS query, but none of those records have the type (such as A) that is specified in the DNS query.

When a DNS resolver caches an NXDOMAIN or NODATA response, this is referred to as negative caching. The duration of negative caching is the lesser of the following values:
- This value—the minimum TTL in the SOA record. In the example, the value is 86400 (one day).
- The value of the TTL for the SOA record. The default value is 900 seconds. For information about changing this value, see Editing records.

When Route 53 responds to DNS queries with an NXDOMAIN or NODATA response (a negative response), you're charged the rate for standard queries. (See "Queries" in Amazon Route 53 Pricing. If you're concerned about the cost of negative responses, one option is to change the TTL for the SOA record, the minimum TTL in the SOA record (this value), or both. Note that increasing these TTLs, which apply to negative responses for the entire hosted zone, can have both positive and negative effects:
- DNS resolvers on the internet cache the non-existence of records for longer periods, which reduces the number of queries that are forwarded to Route 53. This reduces the Route 53 charge for DNS queries.
- However, if you ever erroneously delete a valid record and later recreate it, DNS resolvers will cache the negative response (this record doesn't exist) for a longer period. This lengthens the amount of time that your customers or users can't reach the corresponding resource, such as a web server for acme.example.com.