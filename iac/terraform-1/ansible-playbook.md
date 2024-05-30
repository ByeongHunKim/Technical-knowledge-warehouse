# Ansible Playbook

{% embed url="https://youtu.be/sqj7lSCagO4?si=_Dt4gMKnZZD9HnOB" %}

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption><p>playbook 에제</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (54).png" alt=""><figcaption><p>playbook 실행결과</p></figcaption></figure>

* 약분님 영상을 보고 응용한 상상을 해봤다.



1. eks 에 필요한 모든 aws resources들을 terraform으로 구현한다
2. **Control Node (Local)**:  Control Node  (내 로컬)  에서 SSH를 통해 Managed Node (bastion host ) 로 연결하여 명령을 실행.
3. **Managed Node (Bastion Host)**: Control Node에서 연결되어 설정이 적용되는 노드. Bastion Host는 EKS 클러스터와 상호작용하고 필요한 작업을 수행하는 역할
4. 위의 playbook 처럼 내가 기존에 dev클러스터를 구축할 때 로컬에서 사용하던 명령어들을 playbook에 명시하고 실행하면 bastion host ( EC2 ) 에서 모두 실행한다
5. eks의 모든 aws resources들이 프로비저닝 된 이후에 Bastion Host를 통해 EKS 클러스터에 접근하여 필요한 작업들
   1. eks cluster 연결
   2. istio 설치
   3. load balancer controller 설치
   4. efs csi driver 설치 및 모든 설정
   5. helm 설치
      1. loki, prometheus 설치 ( helm )
      2. pv, pvc 설정

{% hint style="danger" %}
문제는 통신이 원활하게 잘 될 것 인가.. 이다 우선 큰 틀은 이렇게 정했다
{% endhint %}
