## When you are authorized to access AWS resources
authentication은 identity credential을 사용해 aws에 로그인하는 방법이다. aws account root user, iam user, iam role assume과 같은 방법을 사용해 aws에 로그인(authentication)해야 한다.

identity source를 통해 제공되는 credential을 사용해 aws에 federated identity로 로그인할 수 있다. aws iam identity center user, 회사의 single sign-on authentication, google 또는 facebook credential 등이 있다. federated identity로 로그인 하기 위해서 관리자는 먼저 iam role을 사용해 identity federation을 설정해야 한다. federation을 사용해 aws에 접근하는 것은 간접적으로 role을 assume하는 것이다.

사용자 유형에 따라 aws management console, aws access portal을 통해 로그인할 수 있다.

## When you sign-in as an IAM user
iam user는 특정 permission을 갖는 단일 사용자 또는 애플리케이션에 대한 aws 게정 내 identity다. password, access key와 같이 long-term credential을 갖는 iam user를 생성하는 대신 임시 credential을 사용하는 것을 가정한다. 하지만 iam user와 함께 long-term credential이 필요한 특이 케이스가 있는 경우 access key를 rotate하는 것을 권장한다.

iam group은 iam user에 대한 집합을 가리키는 identity다. group으로 로그인하는 것을 불가능하지만 group을 사용해 여러 user에 대한 permission을 지정할 수 있다.

user는 role과 다르다. user는 한 사람 또는 애플리케이션을 고유하게 식별하지만 role은 필요한 모든 사람이 assume할 수 있다. user는 long-term credential을 갖지만 role은 temporary credential을 제공한다.

## When you assume an IAM role
