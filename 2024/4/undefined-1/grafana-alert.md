---
description: 마일스톤2
---

# Grafana 대시보드, Alert 환경 구성



{% hint style="info" %}
혹시 저희 서비스에서 특정 에러가 발생시 지정한 관리자에게 알림을 주고싶은데요 이런 대응을 하려면 어떤 준비를 하면 좋을까요? 혹시 devops쪽에서 어떤 에러 로그 관련해서 슬랙 알림을 줄 수 있을까요?\


현재는 애플리케이션에서 발생하는 얼럿을 받는 기능은 구현되어있지 않아. Loki(로깅) 에서 로그패턴으로 alert설정 가능한지 찾아보았는데 현재 사용중인 loki grafana 에서 alert설정 할 수 있을 것 같다\


1. loki-grafana, grafana의 차이점
2. loki-grafana에 접속해서 어떻게 alert 설정을 하면 슬랙으로 알림을 보내줄 수 있는 가
{% endhint %}
