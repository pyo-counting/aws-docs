## [AWS private global network](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html#what-is-connectivity)
AWS region은 여러 ISP(Internet Service Provider), private global network backbone에 연결되어 region 간 트래픽 전달 시 네트워크 성능을 향상시킨다.

- az 내부 또는 동일 region 내 az 내부 트래픽은 AWS private global network을 통해 라우팅 된다.
- region 간 트래픽은 AWS private global network을 통해 라우팅 된다.