# HELM\_UPGRADE\_VALUES\_FILE로 hpa가 추가되지 않는 원인

### 🚨 문제

* `HELM_UPGRADE_VALUES_FILE: .gitlab/auto-deploy-values.yaml` 이렇게 프로젝트별로 values를 설정하는 파일에 hpa 관련 된 값들을 정의하고 배포한다
* 배포된 이후에 `k get hpa -n {namespace}` 를 했을 때 hpa 리소스가 나와야하는데 나오지 않는 문제

***

### 🧨 원인

1. helm values에 hpa만 명시되어있고 **`Values.resources.requests`** 관련 값이 정의되어 있지 않았다

* resources가 명시된 helm values

```
resources:
  requests:
    cpu: 1
    memory: 512Mi
```

*   resources가 명시된 ns에 배포되어있는 deployment의 상세&#x20;

    ```yaml
    k get deployments production -o yaml
    apiVersion: apps/v1
    kind: Deployment

            resources:
              requests:
                cpu: "1"
                memory: 512Mi
    ```
* resources가 명시되지 않은 ns에 배포되어있는 deployment 의 상세

<pre class="language-yaml"><code class="lang-yaml">k get deployments staging -o yaml
                                                 
apiVersion: apps/v1
kind: Deployment
<strong>
</strong>  resources: {}
 
</code></pre>



1. **`Values.resources.requests` 란**
   1. 파드가 요청하는 최소 리소스의 양을 나타낸다
   2. 일반적으로 CPU와 메모리의 **`requests`** 및 `limits`로 구성
   3. HPA는 CPU 사용률 또는 메모리 사용률을 기준으로 자동으로 스케일링하므로, **`requests`** 값이 있어야 HPA가 올바르게 작동할 수 있다
      1. [HorizontalPodAutoscaler](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/)(HPA)는 워크로드 리소스(예: [디플로이먼트](https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/) 또는 [스테이트풀셋](https://kubernetes.io/ko/docs/concepts/workloads/controllers/statefulset/))를 자동으로 업데이트하며, 워크로드의 크기를 수요에 맞게 자동으로 스케일링하는 것을 목표로 한다.
      2. 수평 스케일링은 부하 증가에 대해 [파드](https://kubernetes.io/ko/docs/concepts/workloads/pods/)를 더 배치
   4. Kubernetes 공식 문서에서도 HPA가 작동하기 위해서는 CPU 또는 메모리의 **`requests`** 설정이 필요함을 강조한다
2. **리소스 요청 추가 후 정상 작동한 이유:**
   1. `HPA`는 `targetCPUUtilizationPercentage`와 같은 목표를 설정할 때 `requests`의 값을 기반으로 목표를 계산한다.
   2. `resources.requests`가 정의되어 있지 않으면 `HPA`는 CPU나 메모리 사용량을 계산할 기준이 없으므로 작동하지 않는다.
   3. `requests`에 CPU 요청 값을 추가하면 `HPA`가 정상적으로 CPU 사용량을 기준으로 스케일링을 수행할 수 있게 된다.

***

### 🔨 해결

1. `auto-deploy-app/templates/db-migrate-hook.yaml`
   1. 위에서 했던 것 처럼 custom auto deploy 에 resource를 생성하는 yaml이 있을거라고 판단
2. `auto-deploy-app/templates/hpa.yaml`
   1. `{{- if and .Values.hpa.enabled .Values.resources.requests -}}` 이 조건을 충족시켜야 함

***

### 🌠 결과

* hpa 뿐만 아니라 resource.requests.cpu가 명시 되어 있어야 hpa 리소스가 배포된다
