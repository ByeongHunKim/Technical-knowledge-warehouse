# play-cluster

## 1. pvc 배포

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: grafana-pvc
  namespace:   # Grafana가 배포될 네임스페이스
  labels:
    app.kubernetes.io/name: grafana
    app.kubernetes.io/instance: grafana-release
    app.kubernetes.io/version: "8.5.0"  # Grafana 버전에 맞추어 수정
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/part-of: grafana
    helm.sh/chart: grafana-8.5.0  # 사용 중인 Helm 차트 버전에 맞추어 수정
  annotations:
    meta.helm.sh/release-name: grafana-release
    meta.helm.sh/release-namespace: common-metric
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi  # Grafana의 데이터 저장 용량 필요에 따라 조정
  storageClassName: nfs-client  # 사용 중인 StorageClass에 맞추어 수정
  volumeMode: Filesystem
```

```bash
k apply -f pvc.yaml
```



## 2. helm upgrade

```yaml
persistence:
  enabled: true
  existingClaim: "grafana-pvc"
  accessModes:
    - ReadWriteOnce
  size: 10Gi
  storageClassName: "nfs-client"
```

```bash
helm upgrade grafana grafana/grafana --namespace common-metric -f values.yaml
```

```bash
k get pod grafana파드 -o yaml
```

* 확인
* name: storage
  * persistentVolumeClaim:&#x20;
    * claimName: grafana-pvc

## 3. Reclaim policy Retain 설정

```bash
k get pv
```

```bash
k edit pv pvc-abe08a3e-1bca-4726-b835-e0d980eb9b34
```

* persistentVolumeReclaimPolicy
  * Retain 으로 변경

## 4. pvc 할당 재확인

```yaml
replicas: 2

persistence:
  enabled: true
  existingClaim: "grafana-pvc"
  accessModes:
    - ReadWriteOnce
  size: 5Gi
  storageClassName: "nfs-client"
```

* ```
  ReadWriteOnce 의미
  ```

```
helm upgrade grafana grafana/grafana --namespace common-metric -f values.yaml
```

* replicas 1로 줄여서 helm upgrade 했을 때도 pvc 할당 잘 되는 것 및 grafana 대시보드에 생성한 것 설정들 유실되지 않았는 지 체크 필요
* 만약 다른 value 값을 수정해서 재배포할 거면 아래 링크 참조

{% embed url="https://github.com/grafana/helm-charts/blob/grafana-7.3.7/charts/grafana/values.yaml" %}
