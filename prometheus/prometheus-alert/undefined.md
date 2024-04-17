# 슬랙 채널 구성

## 리소스 알람 채널 구성

### 클러스터 ( cpu, memory, disk 에 대한 alert rule )

* main ( 24/7 )
  * part-devops-main-critical
* dev
  * part-devops-dev-critical

&#x20;

***

### 프로젝트 ( 네임스페이스에 있는 파드의 cpu, memory 사용률에 대한 alert rule )

* main
  * prj-prjname-main-resource-monitor
* dev
  * prj-prjname-dev-resource-monitor



***

## 프로젝트 로깅 알람 채널 구성

### 프로젝트 ( 로깅 알람 채널 )

* main
  * prj-prjname-main-logging-monitor
* dev
  * prj-prjname-dev-logging-monitor



***

## 프로젝트 배포 알람 채널 구성

### 프로젝트 ( 배포 알람 채널 )

* main
  * prj-prjname-main-integration
* dev
  * prj-prjname-dev-integration
