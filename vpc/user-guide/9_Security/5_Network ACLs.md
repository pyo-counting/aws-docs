network acl(access control list)는 subnet 레벨에서 inbound / outbound 트리팩의 allow / deny를 수행한다.

## [Network ACL basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-basics)
- network acl은 route 53 resolver, Instance Metadata Service (IMDS) 및 아래 목록에 대한 요청 / 응답을 제어할 수 없다.
    - Amazon Domain Name Services (DNS)
    - Amazon Dynamic Host Configuration Protocol (DHCP)
    - Amazon EC2 instance metadata
    - Amazon ECS task metadata endpoints
    - License activation for Windows instances
    - Amazon Time Sync Service
    - Reserved IP addresses used by the default VPC router

## [Ephemeral ports](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports)
The client that initiates the request chooses the ephemeral port range. The range varies depending on the client's operating system.

- Many Linux kernels (including the Amazon Linux kernel) use ports 32768-61000.
- Requests originating from Elastic Load Balancing use ports 1024-65535.
- Windows operating systems through Windows Server 2003 use ports 1025-5000.
- Windows Server 2008 and later versions use ports 49152-65535.
- A NAT gateway uses ports 1024-65535.
- AWS Lambda functions use ports 1024-65535.