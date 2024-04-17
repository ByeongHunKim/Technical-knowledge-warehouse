# prometheus 리소스 alert 세팅

목표

* 쿠버네티스 클러스터의 노드 리소스 ( cpu, memory , disk ) 에 대한 알림을 보낸다
* 프로젝트가 배포된 네임스페이스의 리소스 ( cpu, memory, \[optional] network ) 에 대한 알림을 보낸다

***

* 시간대 별 알림 설정을 통해 dev 환경에 대한 알림은 근무 시간에만 전송되도록 한다
