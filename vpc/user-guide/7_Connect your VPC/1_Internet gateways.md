internet gateway는 public subnet에 존재하는 인스턴스가 public ipv4 주소를 갖는 경우 인터넷에 연결이 가능하도록한다. 반대로 public ipv4 주소를 통해 인터넷상에서 인스턴스에 접근할 수 있다.

internet gateway는 NAT(network address translation)을 사용한다.

## [Configuration for internet access](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#vpc-igw-internet-access)

IP addresses and NAT

인스턴스는 vpc, subnet의 ip 주소만 인식한다. internet gateway는 논리적으로 인스턴스 기준 1:1 NAT을 제공한다. internet gateway를 통해 나가는 트래픽은 public ipv4 주소를 사용한다. 그리고 들어오는 트래픽은 vpc에 들어올 때 private ipv4 주소를 사용한다.