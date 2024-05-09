# helm values에 cpu resource 단위를 1로 했을 때 실제 파드에 1m( 0.001 ) 코어가 부여된 것

### 🚨 문제

```yaml
resources:
  #  limits:
  #    cpu: 100m
  #    memory: 128Mi
  requests:
    cpu: 1
    memory: 512Mi
```

* resources.requests.cpu를 1로 했을 때 바로 1코어를 확보하고 시작하는 줄 알았는데 리소스를 보니 1m로 되어있어서 제대로 적용되지 않는 것으로 추정됨
* 그래서 바로 파드가 요청이 오면 targetCPUUtilizationPercentage 가 100프로가 넘어서 replicas가 순식간에 maxReplicas만큼 늘어나는 줄 알았음

***

### 🧨 원인

[Resource Management for Pods and Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)

* 공식문서를 보니까 1로 명시하면 1코어를 쓰는 것이 맞다. `0.1 = 100m` 이기 때문

***

### 🔨 해결

* cpu: 1000m

```yaml

resources:
  #  limits:
  #    cpu: 100m
  #    memory: 128Mi
  requests:
    cpu: 1000m
    memory: 512Mi
```

* cpu: 1

```yaml

resources:
  #  limits:
  #    cpu: 100m
  #    memory: 128Mi
  requests:
    cpu: 1
    memory: 512Mi
```

* 위에 둘다 deployments에 1로 뜬다

```yaml
k get deployments development -o yaml
apiVersion: apps/v1
kind: Deployment

        resources:
          requests:
            cpu: "1"
            memory: 512Mi
```

* cpu를 0.9로 설정했을 때

```yaml
resources:
  #  limits:
  #    cpu: 100m
  #    memory: 128Mi
  requests:
    cpu: 0.9
    memory: 512Mi
```

* 900m로 값이 들어간다

```yaml
k get deployment staging -o yaml                                    
apiVersion: apps/v1
kind: Deployment
        resources:
          requests:
            cpu: 900m
            memory: 512Mi

```

***

### 🌠 결과

* **`kubectl top pods`** 명령어의 결과로 나타나는 값은 파드가 **현재 사용하고 있는** CPU 및 메모리 리소스 사용량을 나타내는 것. 따라서 \*\*`CPU(cores): 1m`\*\*는 파드가 실제로 1 MilliCPU, 즉 0.001 코어만 사용하고 있다는 의미
* 실제로 사용하고 있는 CPU 리소스 사용량을 나타내는 것이 맞는 지 확인하기 위한 테스트 진행
