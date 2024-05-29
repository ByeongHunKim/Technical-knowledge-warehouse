# \[nGrinder] single endpoint load test script 기반 cpu 사용량 테스트

1\. swagger에서 직접 api 호출

*   before

    * 테스트로 swagger api로 아무 get api call 요청 클릭

    <figure><img src="../../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
*   after

    * 시간이 지난 뒤 다시 값이 원래대로 1m으로 내려옴

    <figure><img src="../../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## 2. nGrinder로 부하주기

<figure><img src="../../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption><p>부하테스트 시작 직후 상태</p></figcaption></figure>

&#x20;

<figure><img src="../../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p>부하 테스트 진행 중</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (7).png" alt=""><figcaption><p>늘어난 cpu 사용량</p></figcaption></figure>

### 2.2 1분30초간 테스트해보고 중단

<figure><img src="../../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### 2.3 다시 CPU Utilzation 이랑, 해당 파드들의 리소스들도 다시 원래대로 낮아짐

<figure><img src="../../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>



***

## 3. prometheus 에서 해당 ns 의 cpu resource monitoring alert rule을 추가

<figure><img src="../../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### 3.1 0.001 코어 ( 1m 밀리코어 ) 로 임계치를 정해서 알림 오는 것 확인

* [알림 설정 가이드](../../../../prometheus/prometheus-alert/)

## 4. 부하 메시지

{% hint style="info" %}
B query 에서 avg() 가 아니라 max()로 하는 게 나을 것 같다
{% endhint %}

<figure><img src="../../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### 4.1 부하 끝나고 그래프 하락

<figure><img src="../../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

