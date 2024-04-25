모든 데이터는 `etcd` nodea에 저장되고 관련 ebs volume은 kms를 사용해 암호화된다. cluster control plane은 multi az를 통해 제공되며 kube-apiserver는 nlb를 통해 제공된다. 그리고 eks는 eni를 vpc subnet에 생성함으로써 control plane과 no가 통신이 가능하도록 한다.

> **Important**:  
> eks 환경에서 `etcd` storage는 [upstream](https://etcd.io/docs/v3.5/dev-guide/limit/#storage-size-limit) 가이드에 따라 최대 8GiB로 제한된다. k8s 1.28부터 `apiserver_storage_size_bytes` metric을 사용해 모니터링이 가능하다.