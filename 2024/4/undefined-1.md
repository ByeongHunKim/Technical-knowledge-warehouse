# 고민들



## ArgoCD 도입 고려



\[목표]

* GitLab Auto Deploy에서 ArgoCD를 사용한 Kubernetes 배포 전략으로 전환하기

\[목적]

* 배포 프로세스의 자동화와 관리 효율성 향상을 통해 더 빠르고 안정적인 어플리케이션 업데이트를 실현하기 위함
* ArgoCD를 도입하면 GitLab Auto Deploy에 비해 구성의 버전 관리와 배포 과정의 시각화가 용이해짐
* ArgoCD의 선언적 접근 방식은 인프라와 어플리케이션의 상태를 Git 리포지토리와 동기화하여, 변경 사항을 자동으로 감지하고 적용하기 때문에, 배포 프로세스를 더 투명하게 관리하고, 오류 발생 시 빠르게 원인을 파악하고 대응할 수 있다

\[세부 요구사항]

1. ArgoCD 설치 및 GitLab CI와의 통합 설정
2. 기존 Kubernetes 매니페스트를 ArgoCD를 이용해 관리할 수 있도록 리포지토리 준비
3. GitLab CI 파이프라인에 ArgoCD 배포 스크립트 추가
4. 배포 프로세스 문서화

\[기타]

* 하지만 기존에는 gitlab auto deploy에 있는 db-migrate-hook 을 사용함으로써 db-migrate-deploy을 수행하고 있어서 argocd를 사용하는게 좋은 선택이 아닐수도 있을 것 같다

***
