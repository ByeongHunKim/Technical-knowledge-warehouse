# HELM\_UPGRADE\_VALUES\_FILE로 hpa가 추가되지 않는 원인

```yaml
HELM_UPGRADE_VALUES_FILE: .gitlab/auto-deploy-values.yaml
```

{% hint style="info" %}
위 처럼 프로젝트에서 helm upgrade values를 참조하고 있다
{% endhint %}



## 문제

### .gitlab/auto-deploy-values.yaml

```yaml
service:
  internalPort: 8080
  externalPort: 8080

ingress:
  annotations:
    kubernetes.io/ingress.class: istio
    kubernetes.io/tls-acme: "true"

livenessProbe:
  initialDelaySeconds: 30
  timeoutSeconds: 3
  path: /health

readinessProbe:
  initialDelaySeconds: 30
  timeoutSeconds: 3
  path: /health

hpa:
  enabled: true
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 75

serviceAccount:
  create: false
  name: nestjs-config-variable-reader-dev
extraEnv:
  - name: APP_ENV
    value: development
```

### hpa.yaml

* 해당 파일은 auto-deploy-app/templates/hpa.yaml 에 있다

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



## 해결

### .gitlab/auto-deploy-values.yaml

```yaml
service:
  internalPort: 8080
  externalPort: 8080

ingress:
  annotations:
    kubernetes.io/ingress.class: istio
    kubernetes.io/tls-acme: "true"

livenessProbe:
  initialDelaySeconds: 30
  timeoutSeconds: 3
  path: /health

readinessProbe:
  initialDelaySeconds: 30
  timeoutSeconds: 3
  path: /health

hpa:
  enabled: true
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 75
# 아래 resource가 없는데 이게 추가되니 배포가 된다
resources:
  requests:
    cpu: 100m


serviceAccount:
  create: false
  name: nestjs-config-variable-reader-dev

extraEnv:
  - name: APP_ENV
    value: development
```

## 원인

* `Horizontal Pod Autoscaler(HPA)`가 배포되지 않은 이유는 `HPA`가 대상이 되는 Deployment나 Pod의 리소스 요청이 정의되어 있지 않기 때문.

#### 원인 분석

1. **리소스 요청이 없는 경우:** `HPA`는 CPU와 메모리 사용량을 기반으로 오토스케일링을 수행한다. 만약 `Deployment`나 `Pod`에서 리소스 요청(`resources.requests`)이 정의되어 있지 않으면, `HPA`는 해당 리소스를 모니터링하지 못해 작동하지 않을 수 있다.
2. **리소스 요청 추가 후 정상 작동한 이유:**
   * `HPA`는 `targetCPUUtilizationPercentage`와 같은 목표를 설정할 때 `requests`의 값을 기반으로 목표를 계산한다.
   * `resources.requests`가 정의되어 있지 않으면 `HPA`는 CPU나 메모리 사용량을 계산할 기준이 없으므로 작동하지 않는다.
   * `requests`에 CPU 요청 값을 추가하면 `HPA`가 정상적으로 CPU 사용량을 기준으로 스케일링을 수행할 수 있게 된다.

#### 해결 방법

helm upgrade values 설정에 `resources.requests`를 추가하여 CPU와 메모리 리소스 요청을 정의하면 된다. 예를 들어, CPU 요청을 100m로 정의하고 메모리 요청을 128Mi로 정의할 수 있다.

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 256Mi
```





