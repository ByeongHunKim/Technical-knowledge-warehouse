# 언어 구조

{% embed url="https://developer.hashicorp.com/terraform/language#about-the-terraform-language" %}

{% embed url="https://malwareanalysis.tistory.com/428" %}

```hcl
<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
  # Block body
  <IDENTIFIER> = <EXPRESSION> # Argument
}
```

* 테라폼 언어는 BLOCK단위로 구성



```hcl
provider "aws" {
  region = "ap-southeast-1"
}
```

* provider 라는 BLOCK TYPE 을 사용하여 provider를 설정



```hcl
resource "aws_vpc" "main" {
  cidr_block = var.cidr_block
}
```

* resource BLOCK은 많이 사용하는 BLOCK TYPE

