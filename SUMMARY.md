# Table of contents

## 2024

* [The DevOps Camino of Akaps](README.md)
* [3월](2024/3/README.md)
  * [29일](2024/3/3-29.md)
* [4월](2024/4/README.md)
  * [업무 일지](2024/4/undefined.md)
  * [업무 기록](2024/4/undefined-1/README.md)
    * [Grafana 대시보드, Alert 환경 구성](2024/4/undefined-1/grafana-alert.md)
  * [1일](2024/4/1.md)
  * [2일](2024/4/2.md)
  * [3일](2024/4/3.md)
  * [4일](2024/4/4.md)
  * [5일](2024/4/5.md)
  * [8일](2024/4/8.md)
  * [9일](2024/4/9.md)
  * [15일](2024/4/15.md)
  * [16일](2024/4/16.md)
  * [17일](2024/4/17.md)
* [5월](2024/5/README.md)
  * [업무일지](2024/5/undefined.md)

## Gitlab

* [Tips](gitlab/tips/README.md)
  * [사전 정의된 CI/CD 변수의 기본 세트](https://docs.gitlab.com/ee/ci/variables/predefined\_variables.html)
* [딥다이브 Gitlab CI/CD](gitlab/gitlab-ci-cd/README.md)
  * [GitLab CI/CD 시작하기](gitlab/gitlab-ci-cd/gitlab-ci-cd/README.md)
    * [.gitlab-ci.yml 파일](gitlab/gitlab-ci-cd/gitlab-ci-cd/.gitlab-ci.yml.md)
    * [Runner](gitlab/gitlab-ci-cd/gitlab-ci-cd/runner.md)
    * [파이프라인](gitlab/gitlab-ci-cd/gitlab-ci-cd/undefined.md)
    * [CI/CD 변수](gitlab/gitlab-ci-cd/gitlab-ci-cd/ci-cd.md)
    * [CI/CD 컴포넌트](gitlab/gitlab-ci-cd/gitlab-ci-cd/ci-cd-1.md)
  * [CI/CD YAML 구문 참조](gitlab/gitlab-ci-cd/ci-cd-yaml.md)
* [CI/CD Notifications](gitlab/ci-cd-notifications/README.md)
  * [CI/CD slack notifiaction 구축작업 결과](gitlab/ci-cd-notifications/ci-cd-slack-notifiaction.md)

## Kubernetes

* [Persistence Volume](kubernetes/persistence-volume/README.md)
  * [play-cluster](kubernetes/persistence-volume/play-cluster.md)
* [Ingress](kubernetes/ingress/README.md)
  * [Ingress란](kubernetes/ingress/ingress.md)
  * [수동으로 ingress 배포하기](kubernetes/ingress/ingress-1.md)
* [Security](kubernetes/security.md)
* [HPA](kubernetes/hpa/README.md)
  * [hpa troubleshooting history](kubernetes/hpa/hpa-troubleshooting-history/README.md)
    * [1. HELM\_UPGRADE\_VALUES\_FILE로 hpa가 추가되지 않는 원인](kubernetes/hpa/hpa-troubleshooting-history/1.-helm\_upgrade\_values\_file-hpa.md)
    * [2. targetCPUUtilizationPercentage 계산은 어떻게 되는가](kubernetes/hpa/hpa-troubleshooting-history/2.-targetcpuutilizationpercentage.md)
    * [3. helm values에 cpu resource 단위를 1로 했을 때 실제 파드에 1m( 0.001 ) 코어가 부여된 것](kubernetes/hpa/hpa-troubleshooting-history/3.-helm-values-cpu-resource-1-1m-0.001.md)
    * [4. pod cpu resources가 할당받은 것을 나타내는 것인 지 현재 사용량을 나타내는 것인지 검증](kubernetes/hpa/hpa-troubleshooting-history/4.-pod-cpu-resources.md)
    * [5. 설정한 hpa를 기반으로 pod auto scaling이 동작하는 지 검증](kubernetes/hpa/hpa-troubleshooting-history/5.-hpa-pod-auto-scaling.md)
  * [HorizontalPodAutoscaler의 behavior 필드 중 stabilizationWindowSeconds 값이 Kubernetes 코드에서 어떻게 사용되는지 분석](kubernetes/hpa/horizontalpodautoscaler-behavior-stabilizationwindowseconds-kubernetes.md)

## prometheus

* [prometheus 리소스 alert 세팅](prometheus/prometheus-alert/README.md)
  * [슬랙 채널 구성](prometheus/prometheus-alert/undefined.md)
  * [alert 환경 구성 가이드](prometheus/prometheus-alert/alert.md)

## Loki

* [loki-grafana alert 세팅](loki/loki-grafana-alert/README.md)
  * [1차 검증 결과](loki/loki-grafana-alert/1.md)
  * [Alert 환경 구성 가이드](loki/loki-grafana-alert/alert.md)

## &#x20;load test

* [nGrinder](load-test/ngrinder/README.md)
  * [nGrinder Test Configuration 값들](load-test/ngrinder/ngrinder-test-configuration.md)
  * [질문리스트](load-test/ngrinder/undefined/README.md)
    * [groovy는 JUnit 스타일에 포함되지 않는건가 ? + GTest가 뭔지 좀 헷갈린다](load-test/ngrinder/undefined/groovy-junit-+-gtest.md)
  * [Script](load-test/ngrinder/script/README.md)
    * [Groovy Script Structure](load-test/ngrinder/script/groovy.md)
      * [reference](https://ckddn9496.tistory.com/120)
      * [Groovy script deep dive](load-test/ngrinder/script/groovy-script-structure/groovy-script-deep-dive.md)
      * [Groovy Script 실행 구조 분석](load-test/ngrinder/script/groovy-script-structure/groovy-script.md)
  * [Test](load-test/ngrinder/test/README.md)
    * [\[nGrinder\] single endpoint load test 하기](load-test/ngrinder/test/ngrinder-single-endpoint-load-test/README.md)
      * [\[nGrinder\] single endpoint load test script 기반 cpu 사용량 테스트](load-test/ngrinder/test/ngrinder-single-endpoint-load-test/ngrinder-single-endpoint-load-test-script-cpu.md)
      * [\[nGrinder\] single endpoint load test script 기반 pod autoscaling 테스트](load-test/ngrinder/test/ngrinder-single-endpoint-load-test/ngrinder-single-endpoint-load-test-script-pod-autoscaling.md)
    * [\[nGrinder\] multi endpoint load test 하기](load-test/ngrinder/test/ngrinder-multi-endpoint-load-test/README.md)
      * [\[nGrinder\] multi endpoint load test script에 정의한 test들이 실행 순서를 보장 받는가?](load-test/ngrinder/test/ngrinder-multi-endpoint-load-test/ngrinder-multi-endpoint-load-test-script-test.md)

## EKS

* [youtube links](eks/youtube-links.md)
* [EKS best practice](eks/eks-best-practice.md)
* [질문 정리](eks/undefined.md)

## Tips

* [Kubernetes](tips/kubernetes/README.md)
  * [k8s reference 1](https://kubetm.github.io/k8s/)
  * [k8s reference 2](https://gasidaseo.notion.site/EKS-Workshop-6b3e478f112a40c3a46fcf67daca9063)

## ideas

* [Notifications to slack channels](ideas/notifications-to-slack-channels/README.md)
  * [botkube](ideas/notifications-to-slack-channels/botkube.md)
* [Terraform](ideas/terraform/README.md)
  * [테라폼 설치](ideas/terraform/undefined.md)
