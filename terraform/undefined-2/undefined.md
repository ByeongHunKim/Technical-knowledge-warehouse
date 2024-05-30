# 변수 정의 방법

{% embed url="https://malwareanalysis.tistory.com/628" %}

{% embed url="https://developer.hashicorp.com/terraform/language/values/variables#type-constraints" %}

Terraform에서 변수를 정의할 때 `default` 값을 지정하면, `terraform apply`나 다른 Terraform 명령을 실행할 때 해당 변수에 대한 값을 명시적으로 제공하지 않아도, Terraform은 자동으로 이 `default` 값을 사용합니다. 이는 코드를 더 유연하고 재사용 가능하게 만들며, 필수적인 입력을 요구하지 않는 상황에서 편리합니다.

#### 예제

`vpc_region` 변수에 `default` 값으로 `"ap-southeast-1"`을 설정했습니다. 이 설정은 다음과 같은 이점을 제공합니다:

* 사용자가 `terraform apply` 실행 시 `vpc_region`에 대한 입력을 요구하지 않습니다. 이는 사용자가 이 값을 제공하지 않던 경우, Terraform이 자동으로 `"ap-southeast-1"`을 사용한다는 것을 의미합니다.
* 사용자가 다른 값을 사용하고 싶다면, 해당 실행에서 명시적으로 다른 값을 제공할 수 있습니다.

#### 사용 예

```hcl
provider "aws" {
  region = var.vpc_region
}
```

위 설정은 `aws` 프로바이더의 리전을 동적으로 설정할 수 있게 해주며, 사용자가 값 입력 없이도 기본 설정에 따라 실행됩니다. 이렇게 하면 코드의 일관성을 유지하면서 사용자의 입력 필요성을 줄일 수 있습니다.

#### 명령줄에서 변수 오버라이드

사용자가 다른 리전을 사용하고 싶은 경우, 명령줄을 통해 이를 쉽게 변경할 수 있습니다. 예를 들어, 리전을 `"us-west-2"`로 변경하려면 다음과 같이 명령을 실행할 수 있습니다:

```bash
terraform apply -var="vpc_region=us-west-2"
```

이렇게 하면 기본값 `"ap-southeast-1"` 대신 `"us-west-2"`가 사용됩니다.

따라서, `default` 값을 설정하면, 변수에 대한 필수 입력 요구를 줄일 수 있으멑, 테라폼 프로젝트의 유연성을 증가시킵니다.



<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>
