# Gateway 주요 특징

Gateway는 Kubernetes Ingress보다 더 강력하고 유연한 기능을 제공합니다. 주요 특징은 다음과 같습니다:

1. 트래픽 진입점: 이 Gateway는 클러스터로 들어오는 외부 트래픽의 진입점 역할을 합니다.
2. 프로토콜 지원: HTTP 트래픽을 처리하도록 구성되어 있습니다 (80번 포트).
3. 호스트 라우팅: 'memecore1-cloud-nestjs-boilerplate-config-variable-staging.dev.memecore.org' 호스트에 대한 트래픽을 처리합니다.
4. Istio IngressGateway 연결: 'istio: ingressgateway' 셀렉터를 통해 Istio IngressGateway와 연결됩니다.
5. 네임스페이스 범위: 'nestjs-boilerplate-config-variable' 네임스페이스에 속합니다.

이 Gateway는 Kubernetes Ingress의 기본 기능을 제공하면서도, Istio의 고급 트래픽 관리 기능(예: 세부적인 라우팅 규칙, 보안 정책, 모니터링 등)을 활용할 수 있게 해줍니다. VirtualService와 함께 사용되어 더 복잡한 라우팅 시나리오를 구현할 수 있습니다.
