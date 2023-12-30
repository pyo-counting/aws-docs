## [ompare security groups and network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/infrastructure-security.html#VPC_Security_Comparison)

| Security group                          | Network ACL                             |
|-----------------------------------------|-----------------------------------------|
| 인스턴스 레벨에서 동작                        | subnet 레벨에서 동작                         |
| 연결된 인스턴스에 대해서 동작                   | 연결된 subnet에 있는 모든 인스턴스에 대해서 동작    |
| allow 규칙만 지원                          | allow / deny 규칙 지원                      |
| 트래픽 허용을 위해 모든 규칙을 평가              | 트래픽 허용을 위해 낮은 번호의 규칙부터 평가        |
| stateful: 규칙과 관계 없이 돌아오는 트래픽은 허용 | stateless: 규칙에 돌아오는 트래픽도 허용되어야 함  |


![](https://docs.aws.amazon.com/images/vpc/latest/userguide/images/security-diagram_updated.png)