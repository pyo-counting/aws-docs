## [Security group basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html#security-group-basics)
- security group은 아래 트래픽을 필터링할 수 없다.
    - Amazon Domain Name Services (DNS)
    - Amazon Dynamic Host Configuration Protocol (DHCP)
    - Amazon EC2 instance metadata
    - Amazon ECS task metadata endpoints
    - License activation for Windows instances
    - Amazon Time Sync Service
    - Reserved IP addresses used by the default VPC router

## [Security group referencing](https://docs.aws.amazon.com/vpc/latest/userguide/security-group-rules.html#security-group-referencing)
규칙에 source 또는 destination에 security group을 참조할 수 있다. 이렇게하면 해당 규칙은 참조한 security group을 사용하는 모든 인스턴스에 적용된다.

security group 규칙에 security group을 참조할 때 아래 규칙이 적용된다.
- 두 security group 모두 동일 vpc 또는 peered vpc에 속해야 한다.
- 참조한 security group의 규칙이 추가되는 것은 아니다.
