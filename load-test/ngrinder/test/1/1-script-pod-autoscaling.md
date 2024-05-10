# 1차 script 기반 pod autoscaling 테스트

#### **요약:**

* 스케일 다운 지연 문제의 원인은 기본 스케일 다운 정책의 안정화 기간(5분) 때문임.
* 이를 해결하기 위해 **`behavior`** 필드를 활용하여 안정화 기간을 줄이는 등의 정책을 커스텀하여 적용할 수 있음



* 현재 deployment에 cpu 100m, memory 128mi, targetCPUUtilizationPercentage: 30 할당

<figure><img src="../../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>



* minReplicas : 1
* maxReplicas : 20

{% hint style="info" %}
기대하는 바는 1로 시작해서 부하를 줬을 때 scale out 되고, 테스트를 종료했을 때 다시 scale down 되는 것
{% endhint %}

<figure><img src="../../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

* 부하테스트 시작

<figure><img src="../../../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

*   pod scale out 확인&#x20;

    * 1 -> 3

    <figure><img src="../../../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>





* TARGETS 값 80%까지 오른 모습

<figure><img src="../../../../.gitbook/assets/image (46).png" alt=""><figcaption><p>desiredReplicas = ceil(1 * (86 / 30)) = ceil(1 * 2.8667) = 3</p></figcaption></figure>

* cpu 사용량이 늘어나는 모습

<figure><img src="../../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>



* 테스트 중지 후 테스트하고 있는 네임스페이스 cpu 사용량 내려가는 그래프 ( prometheus )

<figure><img src="../../../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>



* 테스트 종료 후 파드 별 cpu 사용량 1 밀리코어로 내려온 것 확인
* 그런데 파드가 minReplicas : 1 로 설정했으니 다시 scale down 되어야 하는데 안되고 있음

<figure><img src="../../../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>



{% hint style="danger" %}
scale out는 잘 됐는데, 기대했던 바와 달리 scale down이 바로 이루어지지 않았다
{% endhint %}

{% embed url="https://medium.com/@amirhosseineidy/how-to-make-a-kubernetes-autoscaling-hpa-with-example-f2849c7bbd0b" %}

{% embed url="https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#scaling-policies" %}

* 이 부분을 보면 hpa.yaml에 있는 api v2를 쓰면 behavior를 지정해서 scale down policy를 정할 수 있다

{% embed url="https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#algorithm-details" %}

{% embed url="https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#stabilization-window" %}

* 이 두 부분을 보면 default 값이 5분이라는 것을 알 수 있다

```yaml
behavior:
  scaleDown:
    stabilizationWindowSeconds: 300
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

**기본 스케일 다운 예시 설명**:

* **`300초`** 안정화 기간 동안 스케일 다운 이벤트를 제한.
* **`15초`** 동안 전체 파드의 `100%`까지 스케일 다운할 수 있다.

**기본 스케일 업 예시 설명**:

* **`0초`** 안정화 기간이므로 바로 스케일 업 이벤트를 발생시킨다.
* **`15초`** 동안 총 파드 수의 `100%`까지 늘리거나, 최대`4개`의 파드를 추가한다.
* `selectPolicy`가 `Max`로 설정되어 있으므로 두 정책 중 더 큰 값이 적용된다.

#### behavior 필드 구성

`behavior` 필드에서 스케일 업과 스케일 다운의 동작을 제어할 수 있다

```yaml
behavior:
  scaleDown:
    stabilizationWindowSeconds: <int>
    selectPolicy: <Min|Max|Disabled>
    policies:
    - type: <Pods|Percent>
      value: <int>
      periodSeconds: <int>
  scaleUp:
    stabilizationWindowSeconds: <int>
    selectPolicy: <Min|Max|Disabled>
    policies:
    - type: <Pods|Percent>
      value: <int>
      periodSeconds: <int>
```

**`behavior` 필드**:

* `scaleUp`: 스케일 업 동작을 제어
* `scaleDown`: 스케일 다운 동작을 제어

**`scaleUp` 필드**

* **`stabilizationWindowSeconds`**:
  * 스케일 업의 안정화 기간(초 단위).
  * 지정된 시간 내에 스케일 업 이벤트가 반복되지 않도록 제한.
  * 기본값은 `0초`로 설정되어 있다.
* **`selectPolicy`**:
  * `Max`: 최대 값을 선택
  * `Min`: 최소 값을 선택
  * `Disabled`: 스케일 업을 비활성화
* **`policies`**:
  * 스케일 업 정책 목록을 정의.
  * 각 정책은 다음 두 가지 유형 중 하나를 사용할 수 있다.
    * `Pods`: 파드 수를 기준으로 스케일 업
    * `Percent`: 백분율로 스케일 업

**예시**:

```yaml
scaleUp:
  stabilizationWindowSeconds: 0
  selectPolicy: Max
  policies:
  - type: Pods
    value: 4
    periodSeconds: 60
  - type: Percent
    value: 100
    periodSeconds: 60
```

**`scaleDown` 필드**

* **`stabilizationWindowSeconds`**:
  * 스케일 다운의 안정화 기간(초 단위).
  * 지정된 시간 내에 스케일 다운 이벤트가 반복되지 않도록 제한.
  * 기본값은 `300초`입니다.
* **`selectPolicy`**:
  * `Max`: 최대 값을 선택
  * `Min`: 최소 값을 선택
  * `Disabled`: 스케일 다운을 비활성화
* **`policies`**:
  * 스케일 다운 정책 목록을 정의합니다.
  * 각 정책은 다음 두 가지 유형 중 하나를 사용할 수 있다.
    * `Pods`: 파드 수를 기준으로 스케일 다운
    * `Percent`: 백분율로 스케일 다운

**예시**:

```yaml
scaleDown:
  stabilizationWindowSeconds: 300
  selectPolicy: Min
  policies:
  - type: Pods
    value: 4
    periodSeconds: 60
  - type: Percent
    value: 10
    periodSeconds: 60
```

**예시 메니페스트**:

```yaml
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 0
      selectPolicy: Max
      policies:
      - type: Pods
        value: 4
        periodSeconds: 60
      - type: Percent
        value: 100
        periodSeconds: 60
    scaleDown:
      stabilizationWindowSeconds: 300
      selectPolicy: Min
      policies:
      - type: Pods
        value: 2
        periodSeconds: 60
      - type: Percent
        value: 10
        periodSeconds: 60
```

#### 스케일링 정책 예시 설명

**1. `scaleUp` 필드**

* **`stabilizationWindowSeconds: 0`**:
  * 안정화 기간 없이 바로 스케일 업을 수행.
* **`selectPolicy: Max`**:
  * 스케일 업 정책 중 최대 값을 선택.
* **`policies`**:
  * `Pods`: `60초` 동안 최대 `4개`의 파드를 늘린다.
  * `Percent`: `60초` 동안 최대 `100%`의 백분율로 스케일 업 한다.

**2. `scaleDown` 필드**

* **`stabilizationWindowSeconds: 300`**:
  * 300초 동안 안정화 기간을 적용하여 스케일 다운 이벤트가 잦지 않도록 한다.
* **`selectPolicy: Min`**:
  * 스케일 다운 정책 중 최소 값을 선택한다.
* **`policies`**:
  * `Pods`: `60초` 동안 최대 `2개`의 파드를 줄인다.
  * `Percent`: `60초` 동안 최대 `10%`의 백분율로 스케일 다운한다.

#### `behavior` 설정 활용 전략

* 스케일 업과 스케일 다운의 안정화 기간을 다르게 설정하여 스케일링 이벤트의 빈도를 조절한다.
* `selectPolicy` 필드를 적절하게 활용하여 특정 상황에 최적화된 스케일링 동작을 구현한다.

#### Follow-Up Questions

**Q1: `behavior` 필드에서 `selectPolicy`를 활용한 스케일링 최적화 전략은 무엇인가?**

**Q2: 스케일 업과 스케일 다운의 안정화 기간을 다르게 설정할 때 주의해야 할 점은 무엇인가?**

**Q3: HPA의 `autoscaling/v2beta2` API 버전에서 `behavior` 필드를 효과적으로 사용하는 방법은 무엇인가?**
