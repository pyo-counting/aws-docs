기본적으로 kube-apiserver endpoint는 public network(internet)을 통해 접근이 가능하다. 물론 kube-apiserver는 aws iam, k8s rbac를 이용한 인증/인가가 필요하다.

사용자는 control plane과의 통신을 vpc 내부로 제한할 수 있다. internet을 통한 kube-apiserver 접근은 ip 제한을 하거나 완전하게 internet 접근을 비활성화할 수 있다.

> **Note**:  
> kube-apiserver endpoint는 aws privatelink endpoint가 아니기 때문에 aws vpc console에서 확인이 불가하다.

만약 endpoint private access를 활성화하면, aws eks는 사용자 대신 route 53 private hosted zone을 생성하고 cluster의 vpc에 연결한다. private hosted zone은 eks가 관리하기 때문에 route 53을 통해 사용자가 접근할 수 없다. private hosted zone에 대한 정상적인 트래픽 제어를 위해 vpc의 `enableDnsHostnames`, `enableDnsSupport`가 true, dhcp option set은 dns에 대해 `AmazonProvidedDNS`를 사용해야 한다.

## Modifying cluster endpoint access
- endpoint public access 활성화, endpoint private access 활성화: public network에서는 공인 ip, cluster vpc 내에서는 eni ip로 접근할 수 있다. public DNS에는 공인 ip, route 53 private hosted zone에는 eni ip가 등록된다.
- endpoint public access 활성화, endpoint private access 비활성화: public network, vpv 모두 공인 ip로 접근할 수 있다.
- endpoint public access 비활성화, endpoint private access 활성화: public network에서는 접근이 불가하고, cluster vpc 내에서는 eni ip로 접근할 수 있다. public DNS, route 53 private hosted zone에 모두 eni ip가 등록된다.