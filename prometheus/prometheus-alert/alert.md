# alert 환경 구성 가이드



## 1. pre-requisites

### 1.1 contact-point에 연결할 네이밍 컨벤션이 맞춰진 slack 채널과 webhook URL을 보유하고 있는가?

### 1.2 alert 을 분류할 folder가 생성되어 있는가?



<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
naming convention : \
cluster resources - dev-node-compute-resources\
project ( namespace resources ) - dev-projectName-compute-resources
{% endhint %}



## 2. contact point 생성

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption><p>New contact point 클릭</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption><p>contact point type -> slack 선택</p></figcaption></figure>

1. contact point type -> slack 선택
2. name
   1. slack channel 이름
3. webhook URL
   1. contact point 추가에 필요한 사전 작업 가이드 에서 생성한 webhook URL 입력
   2. Test 버튼 클릭 후 해당 채널에 알림 왔는 지 테스트
   3. Optional Slack settings
      1. Mention Channel
         1. Every channel member
            1. 채널 알림 메시지에 `@channel` 멘션 추가 됨
      2. Disable resolved message
         1. alert rule 조건이 firing → normal status가 되었을 때 resolved 되었다는 메시지가 오지 않음
4. Save contact point 버튼 누르고 생성

***

## 3. \[optional] 시간대 별 알림이 필요한 경우

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption><p>Notification policies → New mute timing 클릭</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

* Time range 제외한 값들은 사진과 값을 모두 같게하고
* Time range 값에 원하는 시간대를 UTC 기준으로 넣은 후 생성

***

## 4. Notification Policy 생성

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption><p>Notification policies → New policy 클릭</p></figcaption></figure>

*   `+ Add matcher` 클릭

    | 종류               | Label   | Value        |
    | ---------------- | ------- | ------------ |
    | cluster resource | cluster | dev          |
    | project resource | project | project name |
* Contact point
  * 2\. contact point 생성 한 것 선택 ( 선택한 슬랙 채널로 alert-rule 발동 시 알림 전송 )
* Overide general timings
  * Group wait
    * 30s
  * Group interval
    * 1m
  * Repeat interval
    * 1m
* Mute timing
  * 3\. \[optional] 시간대 별 알림이 필요한 경우 여기서 생성한 것을 적용하고 싶다면, 선택

***

## 5. alert rule 생성

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption><p>Alerting → New alert rule 선택</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

1. cluster resource 또는 project resource 모니터할 건 지 고르기
2. Query 작성

* project resource query

| query        | cpu                                                                                              | memory                                                                                                    | disk |
| ------------ | ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | ---- |
| A-query      | sum(rate(container\_cpu\_usage\_seconds\_total{namespace="your-namespace”}\[5m])) by (namespace) | sum(avg\_over\_time(container\_memory\_working\_set\_bytes{namespace="your-namespace"}\[10m])) / (1024^3) | 필요없음 |
| B-expression | <p>WHEN → 정해야 함<br>IS ABOBE → 임계치 값 정해야 함</p>                                                    | <p>WHEN → 정해야 함<br>IS ABOBE → 임계치 값 정해야 함</p>                                                             |      |

* cluster resource query

| query        | cpu                                                                                                         | memory                                                                                                                                                                                           | disk                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------ | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| A-query      | (1 - (avg(irate({name=\~"node\_cpu\_seconds\_total\|windows\_cpu\_time\_total",mode="idle"}\[5m])))) \* 100 | (1 - sum({name=~~"node\_memory\_MemAvailable\_bytes\|windows\_os\_physical\_memory\_free\_bytes"}) / sum({name=~~"node\_memory\_MemTotal\_bytes\|windows\_cs\_physical\_memory\_bytes"})) \* 100 | (1 - (((sum(node\_filesystem\_free\_bytes{device!~~"rootfs\|HarddiskVolume.+"}) OR on() vector(0)) + (sum(windows\_logical\_disk\_free\_bytes{volume!~~"(HarddiskVolume.+\|\[A-Z]:.+)"}) OR on() vector(0))) / ((sum(node\_filesystem\_size\_bytes{device!~~"rootfs\|HarddiskVolume.+"}) OR on() vector(0)) + (sum(windows\_logical\_disk\_size\_bytes{volume!~~"(HarddiskVolume.+\|\[A-Z]:.+)"}) OR on() vector(0))))) \* 100 |
| B-expression | <p>WHEN → max()<br>IS ABOBE → 65</p>                                                                        | <p>WHEN → max()<br>IS ABOBE → 70</p>                                                                                                                                                             | <p>WHEN → max()<br>IS ABOBE → 70</p>                                                                                                                                                                                                                                                                                                                                                                                           |

3. Alert evaluation behaivor 작성
   1. e.g 1m for 2m
      1. 1분마다 rule 작동 + 2분간 지속 시 알림

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

1. alert detail 설정
   1. Rule name
      1. cpu
         1. name : cpu-max-warning-alert-rule
      2. memory
         1. memory-max-warning-alert-rule
      3. disk
         1. disk-max-warning-alert-rule
   2. Folder
      1. 분류하기 위해 초반에 생성한 또는 이미 존재하는 folder 선택
   3. Group
      1. cluster
         1. dev-node
      2. project인 경우
         1. project name
   4. summary
      1. 각자 설명 추가
   5. (중요) Custom Labels
      1. 공통
         1. severity = warning
      2. 프로젝트 분류 위한 label 추가 ( 4. Notification policy 생성 할 때 add matcher 에 넣은 값을 같이 넣어줘야 해당 채널로 알림이 전송됨 )
         1. cluster resource 인 경우
            1. cluster = dev
         2. project resource 인 경우
            1. project = projectName
   6. Save 클릭하여 생성
