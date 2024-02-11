route 53 hosted zone은 delegration set이라고도 불리는 4개의 name server와 관련이 있다. 기본적으로 name server의 이름은 ns-2048.awsdns-64.com와 같은 도메인을 갖는다. name server의 이름을 hosted zone과 동일하게 관리하기 위해 white-label name server(또는 vantity name server, private name server)를 구성할 수 있다.

다음 단계에서는 여러 도메인에 대해 재사용할 수 있는 화이트라벨 네임 서버 4개로 구성된 한 세트를 구성하는 방법을 설명합니다. 예를 들어 example.com , example.org 및 example.net 도메인을 소유하고 있다고 가정해 보겠습니다. 이러한 단계를 통해 example.com 에 화이트라벨 네임 서버를 구성하고 example.org 및 example.net 에 재사용할 수 있습니다.


Each Amazon Route 53 hosted zone is associated with four name servers, known collectively as a delegation set. By default, the name servers have names like ns-2048.awsdns-64.com. If you want the domain name of your name servers to be the same as the domain name of your hosted zone, for example, ns1.example.com, you can configure white-label name servers, also known as vanity name servers or private name servers.

The following steps explain how to configure one set of four white-label name servers that you can reuse for multiple domains. For example, suppose you own the domains example.com, example.org, and example.net. With these steps, you can configure white-label name servers for example.com and reuse them for example.org and example.net.

