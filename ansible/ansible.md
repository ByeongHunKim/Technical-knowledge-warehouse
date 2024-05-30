# Ansible 초기 학습 내용

{% embed url="https://youtu.be/s5SGdlwdbpw?si=20rFXPk-A73OyKJV" %}

#### Terraform과 Ansible 코드를 별도의 레포지토리로 관리하는 것의 장점

* **분리된 책임**: Terraform은 인프라를 프로비저닝하고 Ansible은 구성을 관리합니다. 각 도구가 다른 목적을 가지고 있기 때문에, 이를 분리하여 관리하면 더 명확한 책임 구분이 가능합니다.
* **유지 보수 용이성**: 각 레포지토리에 있는 코드가 독립적이기 때문에 변경 사항을 관리하고 유지 보수하는 것이 더 쉬워집니다.
* **협업**: 팀 내에서 다른 사람이 Terraform과 Ansible을 각각 다르게 관리할 수 있어 협업이 용이합니다.
* **버전 관리**: 각 도구의 버전 관리를 독립적으로 할 수 있어 특정 도구에만 영향을 미치는 변경 사항을 쉽게 관리할 수 있습니다.

***

#### 구조 예시

1. **Terraform 레포지토리**
   * 인프라 프로비저닝 코드 (VPC, 서브넷, EKS 클러스터, Bastion Host 등)
   * 출력 변수 설정 (예: Bastion Host IP)
2. **Ansible 레포지토리**
   * 인벤토리 파일 (Terraform 출력 변수를 기반으로 동적 생성)
   * 플레이북 파일 (Istio 설치, ALB Controller 생성, Helm 설치 등)

***

#### Ansible 구조 설명

1. **Control Node**: Ansible이 설치되어 있는 로컬 컴퓨터로, 여기서 Playbook을 작성하고 실행합니다. Control Node에서 SSH를 통해 Managed Node로 연결하여 명령을 실행합니다.
2. **Managed Node (Bastion Host)**: Control Node에서 연결되어 설정이 적용되는 노드입니다. Bastion Host는 EKS 클러스터와 상호작용하고 필요한 작업을 수행하는 역할을 합니다.

#### 예시

* **Control Node**: 사용자의 로컬 컴퓨터 (Ansible 설치)
* **Managed Node**: Bastion Host (Terraform으로 프로비저닝된 EC2 인스턴스)

#### 결론

Ansible을 사용할 때 Control Node는 사용자의 로컬 컴퓨터가 되고, Managed Node는 Bastion Host가 됩니다. 이렇게 구성하면 Control Node에서 Ansible을 사용하여 Bastion Host에 필요한 설정을 적용하고, Bastion Host를 통해 EKS 클러스터에 접근하여 필요한 작업들을 자동화할 수 있습니다.
