# Istio 컴포넌트 별 역할

Istio Gateway, VirtualService, DestinationRule, AuthorizationPolicy, EnvoyFilter 등은 모두 Istio의 커스텀 리소스(Custom Resources)입니다. 이들은 Kubernetes API를 확장하여 Istio의 기능을 정의하고 구성하는 데 사용됩니다.하지만 이 커스텀 리소스들이 직접적으로 트래픽을 전달하지는 않습니다. 대신, 이들은 트래픽 관리를 위한 규칙과 정책을 정의합니다. 실제 트래픽 처리는 다음과 같이 이루어집니다:

1. Istio Control Plane (istiod):
   * 이 커스텀 리소스들의 설정을 감시하고 해석합니다.
   * 이를 Envoy 프록시가 이해할 수 있는 설정으로 변환합니다.
2. Envoy Proxy:
   * 각 서비스의 사이드카로 배포되며, 실제로 트래픽을 처리합니다.
   * Istio Control Plane으로부터 받은 설정에 따라 트래픽을 라우팅, 로드 밸런싱, 필터링합니다.
3. Istio Ingress Gateway:
   * 클러스터로 들어오는 외부 트래픽을 처리하는 특별한 Envoy 프록시입니다.
   * Gateway 및 VirtualService 설정에 따라 트래픽을 내부 서비스로 라우팅합니다.

따라서, 이 Istio 커스텀 리소스들은 트래픽 관리를 위한 "설계도" 역할을 하며, Envoy 프록시가 이 설계도에 따라 실제 트래픽을 처리합니다. 이러한 아키텍처는 정책 정의와 실행을 분리하여 유연성과 확장성을 제공합니다.이미지에서 볼 수 있듯이, istio-ingressgateway가 외부에서 들어오는 트래픽을 받아 내부 서비스(staging-auto-deploy 또는 review-developmen-id0r3d-auto-deploy)로 라우팅하는 것을 확인할 수 있습니다. 이 라우팅 결정은 VirtualService, DestinationRule 등의 설정에 기반하여 이루어집니다.
