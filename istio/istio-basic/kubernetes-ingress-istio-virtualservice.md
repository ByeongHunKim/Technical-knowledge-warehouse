# Kubernetes Ingress와 Istio VirtualService의 관계

Kubernetes Ingress와 Istio VirtualService가 함께 존재하는 경우, 일반적으로 Kubernetes Ingress가 우선적으로 적용됩니다. 이는 다음과 같은 이유로 인해 발생합니다:

1. 트래픽 흐름:\
   Kubernetes Ingress -> Kubernetes Service -> Pod (with Istio sidecar)이 흐름에서 Istio의 VirtualService가 적용되기 전에 Kubernetes Ingress에 의해 트래픽이 이미 라우팅됩니다.
2. 처리 순서:\
   Kubernetes Ingress는 클러스터 수준에서 먼저 처리되며, Istio의 트래픽 관리 기능(VirtualService 등)보다 먼저 동작합니다.
3. 네트워크 스택:\
   Kubernetes Ingress는 일반적으로 클러스터의 네트워크 스택에서 더 상위 레벨에서 동작합니다.
4. 설계 의도:\
   Istio는 주로 서비스 메시 내부의 트래픽을 관리하도록 설계되었으며, Kubernetes Ingress는 외부 트래픽의 진입점 역할을 합니다.
5. 컨트롤 플레인 분리:\
   Kubernetes Ingress와 Istio VirtualService는 서로 다른 컨트롤 플레인에 의해 관리되므로, 서로 직접적인 상호작용이 없습니다.

따라서, Kubernetes Ingress와 Istio VirtualService를 함께 사용할 경우, Ingress의 라우팅 규칙이 우선적으로 적용되고, VirtualService의 세부적인 트래픽 관리 기능은 제한될 수 있습니다.이러한 상황을 해결하기 위해서는 다음과 같은 방법을 고려할 수 있습니다:

1. Kubernetes Ingress 대신 Istio Gateway만 사용하여 외부 트래픽을 관리합니다.
2. Kubernetes Ingress를 Istio Gateway로 마이그레이션합니다.
3. 필요한 경우 Kubernetes Ingress와 Istio Gateway를 분리된 네임스페이스나 서비스 그룹에 적용하여 충돌을 방지합니다.

결론적으로, Kubernetes Ingress와 Istio VirtualService를 함께 사용할 때는 주의가 필요하며, 가능하면 Istio의 트래픽 관리 기능을 최대한 활용하기 위해 Istio Gateway만을 사용하는 것이 좋습니다.
