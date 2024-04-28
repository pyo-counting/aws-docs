여기서는 outbound internet 접근이 없는 EKS cluster 배포 방법을 설명한다.

outbound internet 접근이 없기 위해 아래 요구사항을 만족해야한다.
- cluster는 image를 vpc 내 container registry에서 pull해야 한다.
- cluster는 endpoint private access가 활성화 되어야 한다. 이는 no가 kube-apiserver에 join하기 위해 필요하다. endpoint public access는 옵션이다.
- self-managed node를 실행하기 전에 아래 bootstrap argument를 포함해야 한다. 이 argument는 eks introspection을 무시하고 vpc 내에서 eks API에 대한 접근이 필요하지 않다.
    ``` sh
    --apiserver-endpoint ${cluster-endpoint} --b64-cluster-ca ${certificate-authority}
    ```
- cluster의 `aws-auth`, `ConfigMap`이 vpc 내에서 생성되어야 한다.
- po의 irsa는 aws sts API를 호출하기 때문에 필요에 따라 aws sts vpc endpoint를 생성해야 한다.
- po에서 접근이 필요한 aws service에 대한 vpc interface endpoint를 생성해야 한다. 아래는 일반적으로 사용하는 service와 vpc endpoint를 나타낸다.
    | Service                                                                         |     Endpoint                                                                                               |   |   |   |   |   |
    |---------------------------------------------------------------------------------| --------------------------------------------------------------------------------------------------------|---|---|---|---|---|
    | Amazon EC2                                                                      | com.amazonaws.region-code.  ec2                                                                          |   |   |   |   |   |
    | Amazon Elastic Container Registry (for pulling container images)                | com.amazonaws.region-code.ecr.api, com. amazonaws.region-code.ecr.dkr, and com.amazonaws.region-code.s3 |   |   |   |   |   |
    | Application Load Balancers and Network Load Balancers                           | com.amazonaws.region-code.  elasticloadbalancing                                                         |   |   |   |   |   |
    | AWS X-Ray                                                                       | com.amazonaws.region-code.  xray                                                                         |   |   |   |   |   |
    | Amazon CloudWatch Logs                                                          | com.amazonaws.region-code.  logs                                                                         |   |   |   |   |   |
    | AWS Security Token Service (required when using IAM roles for service accounts) | com.amazonaws.region-code.  sts                                                                          |   |   |   |   |   |


### Considerations
- Any self-managed nodes must be deployed to subnets that have the VPC interface endpoints that you require. If you create a managed node group, the VPC interface endpoint security group must allow the CIDR for the subnets, or you must add the created node security group to the VPC interface endpoint security group.
- If your Pods use Amazon EFS volumes, then before deploying the Amazon EFS CSI driver, the driver's kustomization.yaml file must be changed to set the container images to use the same AWS Region as the Amazon EKS cluster.
- You can use the AWS Load Balancer Controller to deploy AWS Application Load Balancers (ALB) and Network Load Balancers to your private cluster. When deploying it, you should use command line flags to set enable-shield, enable-waf, and enable-wafv2 to false. Certificate discovery with hostnames from Ingress objects isn't supported. This is because the controller needs to reach AWS Certificate Manager, which doesn't have a VPC interface endpoint.
- The controller supports network load balancers with IP targets, which are required for use with Fargate. For more information, see Application load balancing on Amazon EKS and Create a network load balancer.
- Cluster Autoscaler is supported. When deploying Cluster Autoscaler Pods, make sure that the command line includes --aws-use-static-instance-list=true. For more information, see Use Static Instance List on GitHub. The worker node VPC must also include the AWS STS VPC endpoint and autoscaling VPC endpoint.
- Some container software products use API calls that access the AWS Marketplace Metering Service to monitor usage. Private clusters do not allow these calls, so you can't use these container types in private clusters.