---
description: 마일스톤2
---

# Grafana 대시보드, Alert 환경 구성







{% hint style="info" %}
혹시 저희 서비스에서 특정 에러가 발생시 지정한 관리자에게 알림을 주고싶은데요 이런 대응을 하려면 어떤 준비를 하면 좋을까요? 혹시 devops쪽에서 어떤 에러 로그 관련해서 슬랙 알림을 줄 수 있을까요?\


현재는 애플리케이션에서 발생하는 얼럿을 받는 기능은 구현되어있지 않아. Loki(로깅) 에서 로그패턴으로 alert설정 가능한지 찾아보았는데 현재 사용중인 loki grafana 에서 alert설정 할 수 있을 것 같다
{% endhint %}



***

### 질문리스트

* loki-grafana, grafana의 차이점
  * data source가 다른 것
    * loki-grafafa - loki
    * grafana - Prometheus
* loki-grafana에 접속해서 어떻게 alert 설정을 하면 슬랙으로 알림을 보내줄 수 있는 가
  * 같은 grafana여서 `Alerting` 메뉴가 있다

***

### 레퍼런스 링크

{% embed url="https://grafana.com/docs/grafana/latest/alerting/" %}

{% embed url="https://equus3144.medium.com/grafana-notification-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-6d72241c7b27" %}



### pre-requisite

* slack app 추가
  * [https://api.slack.com/apps](https://api.slack.com/apps)
  *

      <figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption><p>Add New Webhook to Workspace로 다른 채널 추가</p></figcaption></figure>

***

## Grafana Notification



### Alerting



#### Contact Points

* `new contact point`

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption><p>contact-point 설정 예시</p></figcaption></figure>

* `name`
  * 해당 check point가 추후에 list 형태로 표기되는 명칭 ( 사용자 임의 지정 값 )
* `contact point type : Slack`
  * alert 알림을 받을 곳
    * `supported contact point integrations`
      * [https://grafana.com/docs/grafana/latest/alerting/fundamentals/contact-points/](https://grafana.com/docs/grafana/latest/alerting/fundamentals/contact-points/)
* `Recipient, Token, WebhookURL`
  * 이 중 `WebhookURL` 에 직접 설정한 slack app의 webhook url을 넣는다
* alert test
  *

      <figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

      <figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

{% hint style="info" %}
alert 계정 설정이 마무리 되었다면, 이제 어떤 alert 알림을 받을 것 인지, metric 을 설정해주는 작업을 함께 설정해줘야 한다
{% endhint %}

* 계정 설정 후에 어떤 metric을 기준으로 Notification을 받을 것 인지 설정 전, Dashboards 에서 `new folder` 를 클릭해 사용자가 직접 설정한 metric을 관리할 폴더 생성
* 폴더에서 새로운 패널 생성
  *

      <figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption><p>패널 설정</p></figcaption></figure>


  * ERROR가 한번이라도 카운트 되는 조건 추가
  * save dashboard

***

* 이제 new alert rule
*



\+

&#x20;                                                                               ㅡ+

