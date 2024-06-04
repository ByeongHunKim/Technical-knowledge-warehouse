# Elastic Load Balancing

{% embed url="https://ongja.notion.site/ELB-Elastic-Load-Balancing-6f5de514ca074ea08477e0c2c635a4fc" %}



## ELB

* aws 에서 제공하는 로드밸런싱 기술

***

### ELB 종류

{% hint style="info" %}
일반적인 VPC 환경에서는 ALB나 NLB를 사용
{% endhint %}

#### Application Load Balancer

* HTTP나 HTTPS와 같이 웹 어플리케이션에 대한 분산 처리를 제공하는 로드 밸런서

#### Network Load Balancer

TCP나 UDP 프로토콜에 대한 포트 정보를 정의하여 네트워크 기반의 분산 처리를 제공하는 로드 밸런서

#### Classic Load Balancer

* EC2 인스턴스의 예전 버전인 EC2-Classic에 대해서도 분산 처리를 제공할 수 있는 이전 세대의 기본적인 로드 밸런서
