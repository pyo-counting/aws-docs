eks platform version은 kube-apiserver의 flag 활성화, k8s patch와 같은 eks control plane과 관련된 버전을 나타낸다. 다른 k8s 버전에 대한 platform version은 독립적이다.

새로운 platform 버전이 사용 가능한 경우
- platform version은 `eks.n+1`와 같은 형태로 증가한다.
- eks는 자동으로 최신 platform version으로 업데이트한다. 하지만 cluster가 현재 버전과 두 버전 이상 차이가 나는 경우 eks는 자동으로 업데이트할 수 없다.
- platform version에 따라 관련 no에 대한 ami를 새로 릴리즈한다. 물론 호환성이 있기 때문에 항상 ami를 바꿔야하는 것은 아니다.