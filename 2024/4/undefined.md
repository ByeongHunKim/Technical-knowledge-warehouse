# 업무 일지

### 4월 1일 월요일

한일

* 기존 프론트 프로젝트들 보안 향상 코드 적용된 이후 배포하여 eks에서 검증
* lazyants 완료
* point-app 은 node\_modules 관련 에러

할일

* point-app 에러 해결 후 배포
* DevOps 파트 마일스톤2 미팅

이슈 및 문의사항

* 없음

***

### 4월 2일 화요일

한일

* DevOps 파트 마일스톤2 미팅
  * 마일스톤 2 티켓 작성
  * roadmap 추가
  * 딥다이브 항목 체크
* devops 지원 업무
  * 개발자분 aws 계정 발급 및 eks 연결 가이드
  * play cluster에 prometheus ingress 수동 배포

할일

* point-app 에러 해결 후 배포 검증
* 마일스톤 2 업무 파악

이슈 및 문의사항

* 주간 운영 담당자 조정

***

### 4월 3일 수요일

한일

* DevOps 파트 마일스톤2&#x20;
  * Grafana 대시보드, Alert 환경 구성 - 로그 패턴 조회 및 Alert 생성
    * alert bot 으로  사용할 slack app 추가 후 테스트 채널 생성
    * contact point, alert rule, notification policy 설정
    * alert test 진행
* devops 지원 업무
  * point-app 배포 지원&#x20;
  * IRSA 설정 업무 지원

할일

* 마일스톤 2 업무
  * Grafana 대시보드, Alert 환경 구성 - 로그 패턴 조회 및 Alert 생성
    * 이슈 원인 파악

이슈 및 문의사항

* Grafana 대시보드, Alert 환경 구성 - 로그 패턴 조회 및 Alert 생성
  * alert test 진행은 잘 되어서 point-api의 logging tracing 시도를 해봤는데 firing 에러가 발생해서 원인 파악 중
    * Failed to load the data source configuration for [Loki](https://logging.dev.memecore.org/datasources/edit/P8E80F9AEF21F6940): Unable to fetch alert rules. Is the Loki data source properly configured?

***

### 4월 4일 목요일

한일

* 마일스톤 2 업무
  * Grafana 대시보드, Alert 환경 구성 - 로그 패턴 조회 및 Alert 생성
    * 어제 이슈 트러블 슈팅하여 알림이 계속 오고 있는 상황

할일

* 마일스톤 2 업무
  * Grafana 대시보드, Alert 환경 구성 - 로그 패턴 조회 및 Alert 생성
    * notification policy 수정하여 그룹화 하도록 테스트
    * 메시지 바꿀 수 있는 지 등 추가 학습

이슈 및 문의사항

* 4월 10일, 11일 휴가&#x20;

***

### 4월 5일 금요일

한일

* 마일스톤 2 업무
* Grafana 대시보드, Alert 환경 구성 - 로그 패턴 조회 및 Alert 생성

할일

* 마일스톤 2 업무
* Grafana 대시보드, Alert 환경 구성 - 로그 패턴 조회 및 Alert 생성

***

### 4월 8일 월요일

한일

* 개발팀 서포트
  * ingress 설정
* 프로젝트 배포 장애 대응
* kubernetes agent 프로젝트 일원화 작업

할일

* 로그 패턴 조회 및 alert 생성 마무리 작업
* 주간 담당자 대응

이슈 및 문의사항

* 없음

***

### 4월 9일 화요일

한일

* 개발팀 서포트
  * 프로젝트 registry 설정 수정 후 재배포
* kubernetes agent 프로젝트 일원화 작업
  * play kubernetes agent 추가
* 리소스 모니터링 관련 리서치

할일

* 주간 담당자 대응
* 리소스 모니터링 관련 리서치

이슈 및 문의사항

* 없음

***

### 4월 15일 월요일

한일

* 주간담당자
  * gas refund 팀 IRSA 설정
  * review tool 프로젝트 build 에러 해결
  * gitlab registry 설정
* alert 환경 구성
  * node cpu 매트릭 정보 기반 알람

할일

* alert 환경 구성
  * 시간대 별 alert 구성 가능한 지 체크
  * 프로젝트(네임스페이스) 별 컴퓨팅 매트릭 정보 수집 가능 여부 체크
  * 슬랙 채널 구성

이슈 및 문의사항

* 없음

***

### 4월 16일 화요일

한일

* 클러스터 리소스 모니터링 alert rule 추가
* 슬랙 채널 구성
* 프로젝트 별 리소스 모니터링
* 시간대 별 alert 구성 방법 리서치

할일

* 시간대 별 alert 구성 테스트
* 리소스 모니터링 문서화

이슈 및 문의사항

* 없음

***

### 4월 17일 수요일

한일

* 시간대 별 alert 구성 및 검증 테스트
* 프로젝트 팀 지원
  * x-exporter, telegram-exporter 배포 지원
    * secret, IRSA 설정
  * lazyants-telegrambot 배포 지원
    * secret 생성

할일

* 시간대 별 alert 구성 및 검증 테스트 마무리
* 리소스 모니터링 문서화
* 프로젝트 팀 지원
  * x-exporter, telegram-exporter 배포 지원&#x20;
    * 특정 ip에서만 요청 보낼 수 있는 istio ingress 설정
  * lazyants-telegrambot 배포 지원
    * secret db url ( point-api dev )
    * IRSA 설정
    * production 배포 설정 ( values )

이슈 및 문의사항

* 없음

***

### 4월 18일 목요일

한일

* 프로젝트 팀 지원
  * play cluster - common-metric grafana pv 설정
  * 업무 종료 후 트러블 슈팅 작성
* 시간대 별 alert 구성 및 검증 테스트 마무리
* 리소스 모니터링 문서화
* CI/CD 업무 티켓 체크 ( 어떻게 풀어나갈 지 )

할일

* CI/CD 개선 업무
  * CI/CD 캐시 환경 구성 관련 리서치

이슈 및 문의사항

* 다음주 화요일 ( 23일 ) 에 외할아버지 첫 기일 성묘 참석을 해야해서 부득이하게 휴가를 써야 할 것 같습니다

***

### 4월 19일 금요일

한일

* CI/CD 개선 업무
  * 가벼운 Flask 프로젝트 세팅 후 Basic Auto DevOps 사용하여 환경 별 배포
  * gitlab 문서 보며 auto devops 학습
  * 1차 개선점 도출
    * staging enabled ( feature 브랜치에서 dev로 merge 시 같은 이미지를 사용하기 때문에 build skip )
    * integration slack alert job 을 custom base gitlab ci에 만들어서 사용 ( gitlab variables에 webhook URL 값을 사용하여 프로젝트별 채널에 알림 가도록 )

할일

* CI/CD 개선 업무
  * gitlab 문서 보며 auto devops 학습
  * flask 프로젝트에 base gitlab-ci 프로젝트의 development 에서 분기한 feature 브랜치 연결 후 개선사항 적용 테스트

이슈 및 문의사항

* 없습니다

***

### 4월 22일 월요일

한일

* CI/CD 개선 업무
  * gitlab 문서 보며 auto devops 학습
  * flask 프로젝트에 base gitlab-ci 프로젝트의 development 에서 분기한 feature 브랜치 연결 후 개선사항 적용 테스트

할일

* CI/CD 개선 업무
  * gitlab CI/CD slack notification 작업
    * CI/CD 성공 시 프로젝트 url, 실패 시 failed job 링크
    * retry 설정
    * build stage 실패 시 알림 추가
  * staging enabled

이슈 및 문의사항

* 없습니다
