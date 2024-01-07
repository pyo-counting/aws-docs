route 53 health check는 AWS 리소스에 대한 모니터링을 수행한다. 사용자는 health check에 대해 cloudwatch alarm을 설정할 수도 있다.

![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/how-health-checks-work.png)

health check와 AWS resource에 대한 dns 레코드를 연결해 DNS failover도 구현할 수 있다.