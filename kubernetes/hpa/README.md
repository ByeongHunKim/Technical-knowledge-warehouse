# HPA

{% embed url="https://saramin.github.io/2022-05-17-kubernetes-autoscaling/" %}

{% embed url="https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/" %}

{% embed url="https://docs.aws.amazon.com/eks/latest/userguide/horizontal-pod-autoscaler.html" %}



Horizontal Pod Autoscaler (HPA)는 Kubernetes 환경에서 Pod의 수를 자동으로 조절하여 클러스터의 리소스 사용률을 최적화하는 기능이다. 이를 설명할 때 어떤 관점으로 접근할지는 상황에 따라 다릅니다. 각 관점에서의 내용을 살펴보자.

#### 1. Kubernetes 관점에서의 HPA

Kubernetes 관점에서 HPA는 리소스 사용률에 기반해 Pod의 수를 조절하는 Kubernetes 객체이다. CPU 및 메모리 사용률뿐만 아니라 커스텀 메트릭을 통해서도 스케일링을 지원한다.

**주요 기능**

* **자동 스케일링**: CPU, 메모리 사용률 또는 커스텀 메트릭을 기반으로 Pod 수를 자동으로 조절.
* **최소/최대 파드 수 설정**: `minReplicas`와 `maxReplicas`로 Pod 수의 최소 및 최대치를 설정.
* **API 버전 차이**:
  * `autoscaling/v1`: CPU 사용률만 지원.
  * `autoscaling/v2`: 메모리 및 커스텀 메트릭을 지원.

**설정 예시**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  annotations:
    meta.helm.sh/release-name: staging
    meta.helm.sh/release-namespace: nestjs-boilerplate-config-variable
  creationTimestamp: "2024-05-09T01:57:58Z"
  labels:
    app: staging
    app.kubernetes.io/instance: staging
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: staging
    chart: auto-deploy-app-2.80.1
    helm.sh/chart: auto-deploy-app-2.80.1
    heritage: Helm
    release: staging
  name: staging-auto-deploy
  namespace: nestjs-boilerplate-config-variable
  resourceVersion: "39297159"
  uid: d5d40b25-453f-43f2-a3d3-35e5d174fdf9
spec:
  maxReplicas: 10
  metrics:
  - resource:
      name: cpu
      target:
        averageUtilization: 70
        type: Utilization
    type: Resource
  minReplicas: 3
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: staging
status:
  conditions:
  - lastTransitionTime: "2024-05-09T01:58:13Z"
    message: recommended size matches current size
    reason: ReadyForNewScale
    status: "True"
    type: AbleToScale
  - lastTransitionTime: "2024-05-09T01:58:29Z"
    message: the HPA was able to successfully calculate a replica count from cpu resource
      utilization (percentage of request)
    reason: ValidMetricFound
    status: "True"
    type: ScalingActive
  - lastTransitionTime: "2024-05-09T04:49:43Z"
    message: the desired replica count is less than the minimum replica count
    reason: TooFewReplicas
    status: "True"
    type: ScalingLimited
  currentMetrics:
  - resource:
      current:
        averageUtilization: 0
        averageValue: 1m
      name: cpu
    type: Resource
  currentReplicas: 3
  desiredReplicas: 3
  lastScaleTime: "2024-05-09T04:44:28Z"
```

**공식 문서**

* [Horizontal Pod Autoscaler 공식 문서](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
* [Horizontal Pod Autoscaler Walkthrough](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)

#### 2. Helm 관점에서의 HPA

Helm을 사용하면 HPA 객체를 쉽게 관리할 수 있다. `values.yaml` 파일을 사용해 설정값을 매개변수화하여 반복적인 배포에 활용할 수 있다.

**주요 기능**

* **템플릿화**: HPA 설정을 매개변수로 추출하여 재사용 가능.
* **버전 관리**: Helm 차트 버전을 통해 배포된 HPA 버전을 관리.
* **자동화 배포**: GitLab CI/CD 파이프라인 또는 다른 CI/CD 도구를 통한 자동화 배포.

**설정 예시**

* `hpa.yaml`:

```yaml
{{- if and .Values.hpa.enabled .Values.resources.requests -}}
{{- if .Values.hpa.metrics }}
apiVersion: autoscaling/v2
{{- else }}
apiVersion: autoscaling/v1
{{- end }}
kind: HorizontalPodAutoscaler
metadata:
  name: {{ template "fullname" . }}
  labels:
{{ include "sharedlabels" . | indent 4 }}
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: {{ template "appname" . }}
  minReplicas: {{ .Values.hpa.minReplicas }}
  maxReplicas: {{ .Values.hpa.maxReplicas }}
{{- if .Values.hpa.metrics }}
  metrics:
{{- toYaml .Values.hpa.metrics | nindent 2 }}
{{- else }}
  targetCPUUtilizationPercentage: {{ .Values.hpa.targetCPUUtilizationPercentage }}
{{- end }}
{{- end}}
```

* `values.yaml`:

```yaml
hpa:
  enabled: true
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
```
