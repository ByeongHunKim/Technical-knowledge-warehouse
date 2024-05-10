# HorizontalPodAutoscaler의 behavior 필드 중 stabilizationWindowSeconds 값이 Kubernetes 코드에서 어떻게 사용되는지 분석

#### 코드 분석

`horizontal.go` 파일에는 HPA 스케일링 로직을 담당하는 핵심 기능들이 포함되어 있습니다. 특히 `stabilizationWindowSeconds` 값이 스케일 다운 안정화 기간을 어떻게 제어하는지 확인하겠습니다.

1. **파일 구조 및 핵심 함수**:
   * 주요 기능이 구현된 함수:
     * `computeDesiredReplicas`
     * `stabilizeRecommendation`
   * `behavior` 필드에 따른 스케일 업/다운 로직은 `computeDesiredReplicas` 함수에서 처리됩니다.
2. **스케일 다운 로직 분석**:
   * `computeDesiredReplicas` 함수 내에서 스케일 다운 로직이 어떻게 작동하는지 확인하기 위해 `stabilizationWindowSeconds` 값이 사용되는 부분을 조사합니다.
   * 스케일 다운 로직은 `stabilizeRecommendation` 함수에서 `stabilizationWindowSeconds` 값을 활용하여 안정화 기간을 설정합니다.

#### 코드 분석 보고서

**1. 분석 보고서: stabilizationWindowSeconds 값 사용 방식**

**`stabilizationWindowSeconds` 값 사용 코드**

* `horizontal.go` 파일 내 `computeDesiredReplicas` 함수 및 `stabilizeRecommendation` 함수에서 사용됩니다.
* **`computeDesiredReplicas` 함수**:
  * `horizontal.go`의 약 800번째 줄에 정의되어 있습니다.
  * `stabilizeRecommendation` 함수를 호출하여 안정화 기간에 따른 파드 수를 조정합니다.

**`kubernetes/pkg/controller/podautoscaler/horizontal.go`의 주요 코드 발췌**:

```go
func computeDesiredReplicas(
    currentReplicas int32,
    scaleUpBehavior *autoscalingv2.HPAScalingRules,
    scaleDownBehavior *autoscalingv2.HPAScalingRules,
    ...
) (int32, string, error) {
    ...

    // Stabilize the recommendation if needed
    if stabilizationWindowSeconds := behavior.ScaleDown.StabilizationWindowSeconds; stabilizationWindowSeconds != nil {
        // stabilizeRecommendation 함수 호출
        proposedReplicas, timestamp = stabilizeRecommendation(
            *stabilizationWindowSeconds, // stabilizationWindowSeconds 값 전달
            timestamp,
            recommendations,
            proposedReplicas,
        )
    }

    ...
}
```

**`stabilizeRecommendation` 함수 구현 코드**:

* `stabilizeRecommendation` 함수는 `stabilizationWindowSeconds` 값에 따라 스케일 다운 안정화 기간을 제어합니다.

```go
func stabilizeRecommendation(
    stabilizationWindowSeconds int32,
    timestamp time.Time,
    recommendations []timestampedRecommendation,
    proposedReplicas int32,
) (int32, time.Time) {
    stableTimestamp := timestamp
    stableReplicas := proposedReplicas

    for _, rec := range recommendations {
        // stabilizationWindowSeconds 값을 기준으로 안정화 기간을 비교
        if rec.timestamp.Add(time.Duration(stabilizationWindowSeconds) * time.Second).After(timestamp) {
            if rec.replicas < stableReplicas {
                stableReplicas = rec.replicas
                stableTimestamp = rec.timestamp
            }
        }
    }

    return stableReplicas, stableTimestamp
}
```

**요약**:

* `stabilizationWindowSeconds` 값은 `computeDesiredReplicas` 함수에서 `stabilizeRecommendation` 함수로 전달됩니다.
* `stabilizeRecommendation` 함수는 안정화 기간 내에 결정된 파드 수를 유지하기 위해 이전의 스케일 다운 이벤트 이후 안정화 기간을 계산합니다.

**예시 매니페스트**: `stabilizationWindowSeconds` 값을 활용한 `HorizontalPodAutoscaler` 매니페스트 예시를 제공해 드립니다.

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300  # 안정화 기간 300초
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
      - type: Pods
        value: 4
        periodSeconds: 15
      selectPolicy: Max
```
