# hpa trouble shooting history

{% hint style="warning" %}
1번 문제를 해결하면 끝날 줄 알았는데, 계속 모르는 내용들 그리고 제대로 알아야 할 것 같은 내용들이 꼬리를 물며 딥다이브를 하게 된 내용을 기반으로 정리하였다.
{% endhint %}



### 1. HELM\_UPGRADE\_VALUES\_FILE로 hpa가 추가되지 않는 원인은?

{% embed url="https://app.gitbook.com/o/qTbrKyJrVXpyaMyqj4WM/s/HF1KkWB9Fesx4B7s0DPh/kubernetes/hpa/hpa-trouble-shooting-history/1.-helm_upgrade_values_file-hpa" %}

### 2. targetCPUUtilizationPercentage 계산은 어떻게 되는가?

{% embed url="https://app.gitbook.com/o/qTbrKyJrVXpyaMyqj4WM/s/HF1KkWB9Fesx4B7s0DPh/~/changes/127/kubernetes/hpa/hpa-trouble-shooting-history/2.-targetcpuutilizationpercentage" %}

### 3. helm values에 cpu resource 단위를 1로 했을 때 실제 파드에 1m( 0.001 ) 코어가 부여된 것 인지 현재 사용량을 의미하는 것 인지 검증

{% embed url="https://app.gitbook.com/o/qTbrKyJrVXpyaMyqj4WM/s/HF1KkWB9Fesx4B7s0DPh/~/changes/127/kubernetes/hpa/hpa-trouble-shooting-history/3.-helm-values-cpu-resource-1-1m-0.001" %}

### 4. pod cpu resources가 할당받은 것을 나타내는 것 인지 또는 현재 사용량을 나타내는 것인지 검증

{% embed url="https://app.gitbook.com/o/qTbrKyJrVXpyaMyqj4WM/s/HF1KkWB9Fesx4B7s0DPh/~/changes/127/load-test/ngrinder/test/1/1-script-cpu" %}



### 5.  설정한 hpa를 기반으로 pod auto scaling이 동작하는 지 검증

{% embed url="https://app.gitbook.com/o/qTbrKyJrVXpyaMyqj4WM/s/HF1KkWB9Fesx4B7s0DPh/~/changes/127/load-test/ngrinder/test/1/1-script-pod-autoscaling" %}
