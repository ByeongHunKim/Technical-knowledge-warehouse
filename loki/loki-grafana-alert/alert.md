# Alert 환경 구성 가이드



| 목차                                |
| --------------------------------- |
| 1. dashboard folder               |
| 2. alert rule                     |
|     2.1. query setting            |
|     2.2. alert evaluation behaior |
|     2.3 add details for alert     |
|     2.4 add custom labels         |
| 3. 실습 환경 삭제                       |

## 1. dashboard folder

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption><p>New folder 클릭</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption><p>네이밍 지정 후 생성</p></figcaption></figure>

* 현재는 gitlab project name으로 convention을 가져간다



***

## 2. alert rule

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption><p>New alert rule</p></figcaption></figure>

### 2.1 query setting

* A, B 모두 세팅 해야한다
* explorer에서 조회할 때 처럼 하면 에러가 난다



### 2.3 alert evaluation behaior

* `Pending` 상태는 Grafana에서 경보 규칙이 설정된 조건을 만족했으나, 아직 설정된 대기 시간(`For` 시간)이 지나지 않아 확정된 경보(`Firing` 상태)로 전환되지 않은 상태를 의미한다. 이는 경보가 발생하려는 초기 단계에서 볼 수 있는 상태이다. 예를 들어 'For 2m'으로 설정되어 있다면, 조건이 충족되고 난 후 2분간의 대기 시간 동안 `Pending` 상태가 유지된다
* 조건이 지속적으로 충족되는 동안 `Pending` 상태가 유지되고, 설정된 대기 시간이 지난 후에 `Firing` 상태로 바뀌게 된다. 이 과정은 설정한 조건과 대기 시간을 확인하는데 중요한 부분이다. 예를 들어, `Evaluate` 조건이 매분 마다 평가되고, `For` 조건이 2분이라면, 이 두 조건이 지속적으로 충족되는지 2분 동안 확인하는 것
* 따라서, `Pending` 상태가 보이다가 갑자기 `Firing`으로 바뀌는 것은 정상적인 동작이다. 이는 Grafana가 설정된 조건과 대기 시간을 정상적으로 처리하고 있음을 의미한다

### 2.4 add custom labels

* 이후에 설정할 notification policy에서 custom label 값인 project에 따라 contact point가 달라지게 설정할 것 이
