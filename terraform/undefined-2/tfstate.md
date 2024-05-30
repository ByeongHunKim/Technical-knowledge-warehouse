# 상태파일 (tfstate)

{% embed url="https://malwareanalysis.tistory.com/430" %}

### 1. 상태파일이란?

{% hint style="info" %}
앞의 사용 순서에서 terraform apply를 통해 코드를 인프라에 반영하면, 반영상태를 파일로 관리함.
{% endhint %}

* 파일 확장자&#x20;
  * tfstate
* 상태 파일
  * json
* 상태파일은 terraform plan과 apply명령어 실행에 영향을 줌
* 코드가 인프라에 반영할 작업과 state파일을 비교하여 작업을 계속 실행할지 넘어갈지 결정



***



### 2. 상태파일 목록 확인

```bash
terraform state list
```

* list 로 보이는 목록은 tf 파일의 BLOCK LABEL
  * 정의 되어 있다고 보이는 것이 아니라, terraform apply 로 코드가 인프라에 반영되어 있어야 함



***



### 3. 상태파일 자세히 보기

```bash
terraform state show <상태이름>
```

* 상태를 특정하여 보고 싶은 경우 사용
  * e.g EC2 instance가 어디 AZ에 있는 지 등

