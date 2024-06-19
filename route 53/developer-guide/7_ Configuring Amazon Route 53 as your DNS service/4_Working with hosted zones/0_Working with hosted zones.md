hosted zone은 reocrd에 대한 container 역할을 한다. record는 특정 도메인, subdomain에 대해 트래픽을 어떻게 라우팅할지에 대한 정보를 포함한다. hosted zone과 관련 도메인은 동일한 이름을 갖는다. hosted zone은 두 가지 타입이 있다:
- public hosted zone: 인터넷 트래픽에 대한 라우팅을 위한 record를 포함한다.
- private hosted zone: AWS vpc 트래픽에 대한 라우팅을 위한 record를 포함한다.