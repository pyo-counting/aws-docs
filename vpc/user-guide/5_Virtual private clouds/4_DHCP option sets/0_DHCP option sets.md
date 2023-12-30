vpc 내 네트워크 디바이스는 DHCP(Dynamic Host Configuration Protocol)을 사용한다. AWS에서 제공하는 DHCP option set을 사용해 여러 기본 설정을 수행할 수 있다. 

## [What is DHCP?](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html)
TCP/IP를 사용하는 디바이스가 서로 통신하기 위해서는 ip를 할당받아야 한다. 과거에는 ip 주소를 수동으로 할당해야했지만 지금은 DHCP 서버를 통해 자동으로 할당할 수 있다.

AWS DHCP는 option set을 통해 네트워크 설정을 구성할 수 있다.