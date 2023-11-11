# 기본 사용법

테라폼을 비롯한 `IaC` 도구들도 각자 고유한 모범 사례(`bast practice`) 가 있고  
그 사례는 모두 다르다.

> main.tf

- `"local_file"` 은 `local` 프로바이더로 파일을 프로비저닝하는데 사용
- `$(path.module)` 은 실행되는 테라폼 모듈의 파일시스템 경로

## init

> terraform init 명령은 테라폼 구성 파일이 있는 작업 디렉터리를 초기화 하는데 사용된다. 이 작업을 실행하는 디렉터리를 루트 모듈이라 부른다.

```sh
$> terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/local...
- Installing hashicorp/local v2.4.0...
- Installed hashicorp/local v2.4.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other

```

이렇게 `terrafrom init` 실행시 `.terraform` 폴더와 `.terraform.lock.hcl` 이  
생성되는것을 볼 수 있다.

마치 `npm init` 시에, `node_module` 와 `package.json`, `package.lock.json` 이  
생성되는것과 비슷하다고 생각이 든다.

- **_`--upgrade`_**

이후 작업자가 버전 변경이 일어나면 `terraform init --upgrade` 수행을 해야한다.

- **_`--no-color`_**

색상없이 출력 가능하다. 다른 환경에서 실행할때, 색상관련된 코드가 생길수 있다

- **_`--json`_**

`json` 으로 출력 가능하다. 책에서는 `jq` 를 사용하여 값을 추출하는데 사용하기도 한다.

> [jq](https://www.44bits.io/ko/post/cli_json_processor_jq_basic_syntax) 는 좋은 `cli tool` 인듯 싶다

### plan & apply

- **_`--plan`_**

적용할 인프라 사항에 관한 실행 계획을 검토하는데 사용

- **_`--detailed-exitcode`_**

환경변수 `$?` 를 통해 정상 종료되었는지 아닌지 확인가능하다

> - **_0_**: 성공(변경없음)
> - **_1_**: 오류
> - **_2_**: 성공(변경있음)

- **_`terraform apply -out=<filename>`_**

`-out=<filename>` 은 `plan` 결과를 저장한 파일이다.
이렇게 생성된 `plan file` 을 사용하여 `terraform apply planFile` 으로  
`apply` 가능하다.

> 실행 계획이 있으므로(`planFile`), 즉시 적용하는 것을 확인할 수 있다
>
> `terraform paln` 과 `terraform apply` 는 생성 예정인 리소스의 생성 계획과  
> 실행을 수행한다.

- **_`--replace`_**

`terraform apply --replace=<targetFile>` 은 대상 리소스 주소를 지정하면  
대상을 삭제 후 다시 생성하는 실행 계획이 발생한다

> `terraform taint <resourceAddress>` 명령으로 재생성했었지만, `v.15.2` 부터는  
> 변경되었다고 한다.

### destroy

`terraform destroy` 를 실행하면, 현재 관리하는 개체를 전부 삭제한다.
`terraform plan --destory --out=<filename>` 형식으로 `destroy plan` 파일을  
내보낸후, 해당 `plan` 을 `terraform apply` 할수도 있다.

> `terraform apply <destroyPlanFile>` 을 하면 관리하는 개체를 삭제한다.

- **_`--auto-approve`_**

`Enter a value` 를 통해 `yes` 를 하지 않더라도, 바로 승인처리 된다.

### fmt

`terraform 내의 prettier` 같은 존재이다.

-- **_`--recursive`_**

재귀적으로 내부의 구성파일 전부 포함해 적용한다.

## HCL(HashiCorp configuration language)

> `IaC`는 수동 프로세스가 아닌 코드를 통해 인프라를 관리하고 프로비저닝 하는 것을

## 말한다.

> 이 코드는 곧 인프라이기 때문에 선언적 특성을 갖게되고 `turing-complate` 언어적  
> 특성을 갖는다. \<중략..\> 이렇게 코드화된 인프라는 주된 목적인 자동화와 더불어,  
> 쉽게 버저닝해 히스토리를 관리하고 작업할수있는 기반을 제공한다.
>
> > `turing-complete` 란 단어가 나오는데, [튜링 완전이란?](https://steemit.com/kr/@brownbears/turing-complete) 에서 확인가능하다.
> > 간단하게 이야기 하면, `가상의 개념 컴퓨터` 이다.

`HCL` 은 일반적은 사람친화적이며, 프로그래밍적인 언어 둘다를 지원하기 위해  
만들어진 새롭게 만들어진 언어이다.

확실히 익숙해지면, 편해보이기는 하다.
`JSON` 은 감싼 `bracket` 이 정말 보기 불편하다.

마치 `python` 처럼 생겨먹었다.

## 테라폼 블록

`terraform` 을 구성하는 선언블록이다.

> 테라폼의 구성을 명시하는데 사용된다.
> ex\) `terraform.version`, `terraform.required_provider` 등등...
>
> 책에서 제공해주는 예시이다.

```tf
terraform {
  # 테라폼 버전
  required_version = "~> 1.3.0"
  # 프로바이더 버전
  required_providers {
    random = {
      version = ">= 3.0.0, < 3.1.0"
    }
    aws = {
      version = "4.2.0"
    }
  }
  # Colud/Enterprice 같은 원격 실행 정보
  cloud {
    organization = "<ORG_NAME>"
    workspaces {
      name = "my-workspacke"
    }
  }
  # state 보관 위치 지정
  backend "local" {
    path = "relative/path/to/terraform.tfstate"
  }
}

```

이렇게 구성되는데, 버전 관리 관련해서는 일반적은 `samVar` 방식을 따른다.
조금 살펴보아야 할건, 버전 [Version Constraint Syntax](https://developer.hashicorp.com/terraform/language/expressions/version-constraints) 인데 다음의 구성요소로 이루어져 있다.

위의 내용을 대략 살펴보자면

- **_`=`_**: 정확히 해당 버전만을 허용한다. 다른 `condition` 과는 사용 불가

- **_`!=`_**: 해당 버전만을 제외한다.

- **_`>, >=, <, <=`_**: 지정된 버전에 대해서 비교한다.

- **_`~>`_**: 이 방식이 조금 특이한데 `rightmost version component to increment`  
  라고 표현한다. 이말은 가장 오른쪽 버전의 증가만 허용한다는 것이다. `samVar` 같은  
  경우 `1.0.4` 이나, `1.1` 처럼 사용도 가능한데, 이때 가장 오른쪽 숫자의 증가된 값만 허용한다는 것이다.

> - **_`~> 1.0.4`_**: `1.0.5` 그리고 `1.0.10` 은 허용하지만, `1.1.0` 은 허용하지 않는다.
> - **_`~> 1.1`_**: `1.2` 그리고 `1.10` 은 허용하지만, `2.0` 은 허용하지 않는다.

### reqired_verion

`terraform version` 을 지정한다.
지정 조건은 위에서 본바와 같이 [Version Constraint Syntax](https://developer.hashicorp.com/terraform/language/expressions/version-constraints) 를 따른다.

```tf
# main.tf
terraform {
  # 테라폼 버전
  required_version = "~> 1.3.0"
}
```

방식으로 작성 가능하다
현재 `terraform` 실행 버전에 맞추어 실행하게 되며, 만약 `1.3.0` 보다 낮으면  
`error` 가 발생할것이다.

[tfenv](https://github.com/tfutils/tfenv) 라는 `terrafrom version manager` 가 있다고 한다.

### required_providers

`Provider` 버전을 사용한다.
`< v0.13` 에서는 `Provider` 블록을 제공했다고 하는데, `>= v0.13` 에서는  
`terraform module` 내의 `required_providers` 로 정의된다고 한다.

```tf
terraform {
  required_providers {
    random = {
      version = ">= 3.0.0, < 3.1.0"
    }
    aws = {
      version = "4.2.0"
    }
  }
}

```

각 `provider` 들은 `docker hub` 처럼 `Hacicorp` 가 제공하는 [terrafrom registry](https://registry.terraform.io/) 에서 제공된다.

### Colude black

> `Terraform Cloud`, `Terraform Enterprice` 는 `CLI`, `VCS`, `API` 기반의  
> 실행방식을 지원하고 `cloud` 블록으로 선언한다.
>
> `cloud` 블록은 `>= v1.1` 에 추가된 선언으로 기존의 `State` 저장소를 의미하는  
> `backend` 의 `remote` 항목으로 설정했다.

- `remote` 방식

```tf
# Using a single workspace:
terraform {
  required_version = ">= 1.1.0"
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "~> 3.0.2"
    }
  }
  backend "remote" {
    hostname = "app.terraform.io"
    organization = "company"

    workspaces {
      name = "my-app-prod"
    }
  }
}
```

- `cloud 방식`

```tf

terraform {
  required_version = ">= 1.1.0"
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "~> 3.0.2"
    }
  }
  cloud {
    hostname = "app.terraform.io"
    organization = "company"

    workspaces {
      name = "my-app-prod"
    }
  }
}

provider "azurerm" {
  features {}
}

```

### backend block

> 백엔드 블록의 구성은 테라폼 실행시 저장되는 `state` 의 저장위치를 선언한다.

`state` 는 생성한 인프라의 현재 상태를 저장하는 파일이다.
그러므로, 이러한 `state` 의 리소스를 추적하여 변경사항을 식별할 수 있다.

이때 `backend block` 은 외부 `backend strorage` 를 만들어, 이 `state` 를 외부에  
저장할 수 있고, 협업도 가능하다.

> 어찌 되었건, `terraform` 은 이 생성된 `state` 를 추적하여, 변경사항을 식별해야  
> 제대로 동작한다.

만약, 2 사람이 동시에 리소스를 수정하기 위해 `terraform apply` 를 진행했다고 하자,
이때 먼저 실행한 사람이 `apply` 중이면 다른사람은 `apply` 못하게 `Error` 가 발생한다.

`.terraform.lock.hcl` 잠김파일이 생성되며, 지금 `apply` 중임을 알린다.

- **_`--lock=false`_**: 이 옵션을 사용하여 잠금을 무시할수 있다고 한다.

> `backend` 가 설정되면 다시 `init` 명령을 수행해 `state` 위치를 재설정해야 한다.

```tf

terraform {
  backend "local" {
    path = "state/terraform.tfstate"
  }
}

```

`terraform init` 을 실행하면  
`state` 폴더안에 `terraform.tfstate` 가 생성된다.

> `backend` 를 전환하는 것은 `state` 관리가 되는 저장소를 선택하는 것과 같다.

여기서 `cloud` 와 `backend` 차이점을 집고 넘어가야 겠다.

| 기능          | terraform cloud                                   | terraform backend |
| :------------ | :------------------------------------------------ | :---------------- |
| 제공업체      | HashiCorp                                         | Terraform         |
| 지정하는 항목 | state 파일 및 Terraform 구성                      | state 파일만      |
| 엑세스 방법   | Terraform Cloud UI, CLI, API                      | Terrafrom CLI     |
| 제공기능      | 협업, 버전관리, 모니터링, 알림, 보안 및 규정 준수 | 없음              |

## Resource

`resource` 블록은 리소스 유형을 정의한다.

- `_` 를 기준으로 앞은 `provider이름`, 뒤는 `provider 에서 제공하는 리소스 유형` 을  
  의미한다.

- `resource 유형` 뒤에는 식별가능한 고유한 이름을 사용한다.

> ex) resource "local_file" "고유한 이름" { ... }

- `block` 내부에는 리소수 유형에 맞는 인수들을 적용한다.

> 이렇게 설명하는 이유가, 다른 `block` 과는 다르게 `resorce` 가 많아서  
> 구성 인자가 다 다르다.

### 종속성

> 프로비저닝되는 각 요소의 생성순서를 구분 짓는다

`docker compose` 의 `depends_on` 이라고 생각하면 될듯하다.

```tf

resource "local_file" "abc" {
  content = "123!"
  filename = "${path.module}/abc.txt"
}

resource "local_file" "def" {
  content = "456!"
  filename = "${path.mocule}/def.txt"
}

```

이렇게 하면 병렬로 실행된다
여기서 `abc` 가 실행되고난 다음에 `def` 가 실행되도록 하려면
다음처럼 변경한다.

```tf

resource "local_file" "abc" {
  content = "123!"
  filename = "${path.module}/abc.txt"
}

resource "local_file" "def" {
  content = local_file.abc.content
  filename = "${path.mocule}/def.txt"
}

```

> 특정 리소스의 속성 값이 필요한 경우 해당 리소스가 우선 생성되야 하므로  
> 생성에 대한 종속성이 생겨 프로비저닝의 순서가 발생한다.

`terraform graph` 명령을 사용하면 테라폼의 수행 단계를 볼수 있다.

`depends_on` 을 사용해서 종속성을 적용할수도 있다.

```tf

resource "local_file" "abc" {
  content = "123!"
  filename = "${path.module}/abc.txt"
}

resource "local_file" "def" {
  depends_on = [
    local_file.abc
  ]
  content = "456!"
  filename = "${path.mocule}/def.txt"
}

```

간단하다.

### 리소스 속성 참조

마치 객체의 프로퍼티 작성하는것과 비슷하다.
여기서 `인수` 와 `속성` 의 의미가 다르다고 나온다.

- `인수`: 리소스 생성시 사용자가 선언한 값
- `속성`: 리소스 생성 이후 획득 가능한 리소스 고유 값

### lifecycle

> 해당 리소스의 생명주기를 설정한다.

- **`create_before_destroy`**: 생성한 이후 삭제.

책에서는 주의점에 대해서 설명한다

```tf
resource "local_file" "abc" {
  content = "123!!!!"
  filename = "${path.module}/abc.txt"
  lifecycle {
    create_before_destroy = false
  }
}
```

이렇게 `terraform apply` 로 적용하면, `abc.txt` 가 생성된다.
생성된 상태에서

```tf
resource "local_file" "abc" {
  content = "변경"
  filename = "${path.module}/abc.txt"
  lifecycle {
    create_before_destroy = true
  }
}
```

로 하고, `terraform apply` 를 다시 실행시켜본다.
해당 `abx.txt` 는 없어진다.

작업순서는 이렇다.

`abc.txt` 변경, 그리고 같은 이름을 가진 `abc.txt` 삭제.
그러므로, 이러한 주의점을 알고 설정하는것이 중요하다.

- **`prevent_destroy`**: 삭제하지 못하도록 막는다.

말 그대로 삭제를 하지 못하게 한다.

```tf

resource "local_file" "abc" {
  content = "test"
  filename = ${path.module}/abc.txt

  lifecycle {
    prevent_destroy = true
  }
}

```

이후에 `terraform apply` 을 실행하면, 삭제 이후에 새로운 `abc.txt` 를 생성못한다.
`prevent_destroy` 를 설정했기 때문이다.

```sh
local_file.abc: Refreshing state... [id=a94a8fe5ccb19ba61c4c0873d391e987982fbbd3]
╷
│ Error: Instance cannot be destroyed
│
│   on main.tf line 1:
│    1: resource "local_file" "abc" {
│
│ Resource local_file.abc has lifecycle.prevent_destroy set, but the plan calls for this resource
│ to be destroyed. To avoid this error and continue with the plan, either disable
│ lifecycle.prevent_destroy or reduce the scope of the plan using the -target flag.
```

이러한 에러가 나온다.

- **`ignore_changes`**: 수정시 변경 못하게 막는다.

```tf

resource "local_file" "abc" {
  content = "test3" #변경했다
  filename = "${path.module}/abc.txt"
  lifecycle {
    ignore_changes = [content]
  }
}

```

이렇게 하면, `content` 가 변경되더라도 무시한다.
`ignore_changes = all` 로 하면 모든 변경을 무시한다

- **`precondition`**: 프로비저닝 이전에 검증한다

```tf

variable "file_name" {
  default = "step0.txt"
}

resource "local_file" "step6" {
  content = "test5"
  filename = "${path.module}/${var.file_name}"
  lifecycle {
    precondition {
      condition = var.file_name == "step6.txt"
      error_message = "파일이름이 step6.txt 가 아니에요"
    }
  }
}

```

```sh
│ Error: Resource precondition failed
│
│   on main.tf line 10, in resource "local_file" "step6":
│   10:       condition = var.file_name == "step6.txt"
│     ├────────────────
│     │ var.file_name is "step0.txt"
│
│ 파일이름이 step6.txt 가 아니에요
```

`step6.txt` 아니라서 프로비저닝 이전에 `error` 가 발생한다.

- **`postcondition`**: 프로비저닝 이후에 결과를 검증한다

```tf

resource "local_file" "step7" {
  content = ""
  filename = "${path.module}/step7.txt"

  lifecycle {
    postcondition {
      condition = self.content != ""
      error_message = "content 가 비어있어요!!"
    }
  }
}

output "step7_content" {
  value = local_file.step7.id
}

```

```sh
╷
│ Error: Resource postcondition failed
│
│   on main.tf line 7, in resource "local_file" "step7":
│    7:       condition = self.content != ""
│     ├────────────────
│     │ self.content is ""
│
│ content 가 비어있어요!!
╵
```

`error` 가 발생하는것을 볼 수 있다.

## 데이터 소스

> `data source` 는 테라폼으로 정의되지 않은 외부 리소스 또는 저장된 정보를  
> 테라폼 내에서 참조할때 사용한다.

이 `data source` 가 가장 헷갈리는 부분이었다.
이게 처음에는 이해한 내용이, `외부 리소스` 를 가져오는 것이었다.
`terraform` 내부의 `외부 리소스` 는 언제든지 번경가능한 `data`이므로,  
이러한 `data` 를 안전하게 가져와, `infra` 구축하는데 사용할수 있도록 하는  
것이라고만 생각했다.

그런데, `local data` 에서도 사용한다?

```tf

data "local_file" "abc" {
  filename = "${path.module}/abc.txt"
}

```

> 이 구문을 보고 첫번째 든 의문은 `resource` 랑 무슨 차이야?
> 라고 생각이 들었다.

분명 `의도적으로` 사용된 `data` 를 사용하여 처리한것일텐데,  
`local` 상에서도 처리가 된다?

`local` 에서 사용되는 이유가 있을까?
그렇다 몇가지 이유가 존재한다.

[local-only-data-sources](https://developer.hashicorp.com/terraform/language/data-sources#local-only-data-sources) 에 해당 내용이 적혀있다.

> some specialized data sources operate only within Terraform itself, calculating some results and exposing them for use elsewhere.

이렇게 적혀있는데, 간단하게 이야기하면, `terraform` 내부에서 만들어진 `data` 를  
외부에 노출시킬때도 사용된다는 것이다.

이때, `local` 메서만 작동하는 `data-source` 들은 예를 들어, [rendering templates](https://registry.terraform.io/providers/hashicorp/template/latest/docs/data-sources/file), [local_file](https://registry.terraform.io/providers/hashicorp/local/latest/docs/data-sources/file), [aws_iam_policy_document](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) 가 있다. 이것들 외에 더 있다.

그럼, 다음처럼 정리하자.

1. `data source` 는 외부의 동적 `data` 를 받을 목적으로 사용된다.
2. `data source` 는 내부의 `data` 를 외부에 노출시킬 목적으로 사용된다.

> 추후에, `docs` 를 보며 다시 정리해야 겠다.
> 이 장의 `data source` 는 너무 간단하게 설명하고 넘어간다..

사용되는 메타인수는 다음과 같다

- **`depends_on`**: 종속성을 정의한다.
- **`count`**: 선언된 개수에 따라 여러 데이터 소스 선언
- **`for_each`**: map or set 타입의 데이터배열의 값을 기준으로 여러 리소스 생성
- **`provider`**: 프로바이터 정의
- **`lifecycle`**: 데이터 소스의 생명주기관리

```ts

data "aws_availability_zones" "available" {
  state = "available"
  // 현재 리전의 사용가능한 가용영역에 대한 리스트
  // ap-northeast-2 리전에 ap-northeast-2a, ap-northeast-2b, ap-northeast-2c
  // 라는 총 3개의 가용영역이있다고 해보자
}

resource "aws_subnet" "primary" {
  availability_zone = data.aws_availability_zones.available.names[0]
  // 여기서 `availability_zone` 은 `ap-northeast-2a` 로 선택된다
}

resource "aws_subnet" "secondary" {
  availability_zone = data.aws_availability_zones.available.names[1]
  // 여기서 `availability_zone` 은 `ap-northeast-2b` 로 선택된다
}

```

[Data Source: aws_availability_zones](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/availability_zones) 에서 내용확인 가능하다

## 입력 변수

`terraform` 에서 속성값을 정의가능한데, 여러 인프라를 생성하는데 사용된다.
그래서 이러한 변수를 `Input variable` 이라고 한다.

변수 선언은 간단하다.

```tf
variable "<이름>" {
  <인수> = <값>
}
```

이렇게 하면 `Input vairable` 선언이 가능하다.
변수에 사용가능한 `meta argument` 는 다음과 같다

- **`default`**: 기본 값을 지정한다.
- **`type`**: 값의 타입을 지정한다.
- **`description`**: 변수에 대한 설명을 작성한다.
- **`validation`**: 변수에 제약조건을 사용하여 유효성 검사규칙을 정의한다.
- **`sensitive`**: 민감한 변수값임을 알림, 출력문에 노출 제한
- **`nullable`**: 변수에 null 값 허용

### 변수유형

프로그래밍 언어에서 변수의 타입은 중요하다.
`HCL`역시 타입이 존재한다.

- **`string`**
- **`number`**
- **`bool`**
- **`any`**
- **`list(<TYPE>)`**
- **`map(<TYPE>)`**
- **`set(<TYPE>)`**
- **`object(<ATTR_NAME> = <TYPE>, ...)`**
- **`tuple(<TYPE>, ...)`**

[Type Constraint](https://developer.hashicorp.com/terraform/language/values/variables#type-constraints) 에서 확인 가능하다.

```tf
variable "string" {
  type        = string
  description = "var String"
  default     = "myString"
}

variable "number" {
  type    = number
  default = 123
}

variable "boolean" {
  default = true
}

variable "list" {
  default = [
    "google",
    "vmware",
    "amazon",
    "microsoft"
  ]
}

output "list_index_0" {
  value = var.list.0
}

output "list_all" {
  value = [
    for name in var.list : upper(name)
  ]
}

variable "map" { # Sorting
  default = {
    aws   = "amazon",
    azure = "microsoft",
    gcp   = "google"
  }
}

variable "set" { # Sorting
  type = set(string)
  default = [
    "google",
    "vmware",
    "amazon",
    "microsoft"
  ]
}

variable "object" {
  type = object({ name = string, age = number })
  default = {
    name = "abc"
    age  = 12
  }
}

variable "tuple" {
  type    = tuple([string, number, bool])
  default = ["abc", 123, true]
}

variable "ingress_rules" { # optional ( >= terraform 1.3.0)
  type = list(object({
    port        = number,
    description = optional(string),
    protocol    = optional(string, "tcp"),
  }))
  default = [
    { port = 80, description = "web" },
  { port = 53, protocol = "udp" }]
}
```

이 코드는 [3-32](https://github.com/terraform101/terraform-basic/blob/main/3-32.tf) 에서 확인가능하다.

### 유효성 검사

`validation` 블록을 사용하여 `condition` 에 지정되는 규칙이 `false` 이면  
`error_message` 를 반환한다.

```tf

variable "image_id" {
  type = string
  description = "The id of the machine image (AMI) to use for the server"

  validation {
    condition = length(var.image_id) > 4
    error_message = "이미지 id 의 글자갯수가 4 보다 작아야 합니다."
  }
  validation {
    condition = can(regex("^ami-", var.image_id))
    error_message = "이미지 아이디는 반드시 "ami-" 로 시작해야 합니다."
  }
}

```

[can 함수](https://developer.hashicorp.com/terraform/language/functions/can) 는 표현식을 평가하고, 표현식이 `error` 없이 결과를 생성했는지 여부에 따라 `boolean`  
값을 `return` 한다.

### 변수 참조

`var.<이름>` 으로 변수 참조가 가능하다.

### `sensitive`

변수의 민감여부에 따라 `sensitive` 를 사용할 수 있다.
변수에 `sensitive = ture` 를 추가하면, 해당 값을 표시하지 않고, `(sensitive)` 로  
나타난다.

```tf

variable "pass" {
  default = 1234
  sensitive = true
}

resource "local_file" "abc" {
  content = var.pass
  filename = "${path.module}/abc.txt"
}

```

```sh
$> terraform apply
  # local_file.abc will be created
  + resource "local_file" "abc" {
      + content              = (sensitive value)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./abc.txt"
      + id                   = (known after apply)
    }
```

`content` 의 값이 `sensitive value` 로 나타나는 것을 볼 수 있다.
출력값에 해단 값이 출력되길 원치 않을때 사용한다.

### 변수 입력 방식과 우선순위

> `vairable` 의 목적은 코드 내용을 수정하지 않고 테라폼의 모듈적 특징을  
> 통해 입력되는 변수로 재사용성을 높이는데 있다.
>
> 입력 변수라는 명칭에 맞게 사용자는 프로비저닝 실행시에 원하는 값으로  
> 변수에 정의할 수 있다.
>
> 선언되는 방식에 따라 변수의 우선순위가 있으므로, 이를 적절히 사용해  
> 로컬 환경과 빌드 서버 환경에서의 정의를 다르게 하거나, 프로비저닝 파이프라인을
> 구성하는 경우 외부 값을 변수에 지정할 수 있다.

아래는 우선순위가 높은 순이다.

- `CLI` 실행시 `-var` 인수에 지정, `-var-file` 로 파일 지정
- `*.auto.tfvars.json` 에 정의된 변수
- `*.auto.tfvars` 에 정의된 변수
- `terrafrom.tfvars` 에 정의된 변수
- 환경변수
- `variable` 의 `default`
- 실행후 입력

### local 선언

`locals` 블록의 변수는 전체 루트 모듈내에서 유일해야 한다.

```tf

variable "prefix" {
  default = "hello"
}

locals {
  name = "terraform"
  content = "${var.prefix} ${local.name}"
  my_info = {
    age = 20
    region = "KR"
  }
  my_nums = [1, 2, 3, 4, 5]
}

locals {
  content = "centent2" # Error occur
}

```

`locals` 의 변수 참조는 `local.content` 으로 참조한다.
여기서 중요한건, `root module` 내의 유일한 변수들을 지정한다는 것이다.

그러므로, `main.tf` 에 지정한 `local` 변수를 다른 파일인 `sub.tf` 에서도
참조 가능하다.

간단하게 말하면, `locals` 의 변수는 전역변수로서 역할을 하는듯하다.

> 로컬 값이 파편화되어 유지보수가 어려워질 수 있으므로 주의가 필요하다

## 출력(OUTPUT)

프로비저닝 이후에 속성값을 확인하는 용도로 사용되며, 워크스페이스 및 테라폼 모듈간에  
데이터 접근 요소로도 활용된다.

```tf

output "instance_ip_addr" {
  value = "http://${aws_instance.server.private_ip}"
}

```

- **`description`**: 값 설명
- **`sensitive`**: 민감한 값 설정
- **`depends_on`**: 종속성 설정
- **`precondition`**: 출력전에 조건 검증

> 주의할 점은, output 결과에서 리소스 생성 후 결정되는 속성 값은
> 프로비저닝이 완료되어야 최종적으로 결과를 확인할 수 있고  
> `terraform plan` 단계에서는 적용될 값을 출력하지 않는다.

```tf

resource "local_file" "test" {
  content = "test"
  filename = "${path.module}/test.txt"
}

output "file_id" {
  value = local_file.test.id
}

output "file_abspath" {
  value = abspath(local_file.test.filename)
}

```

```sh
  # local_file.test will be created
  + resource "local_file" "test" {
      + content              = "test"
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./test.txt"
      + id                   = (known after apply)
    }

Plan: 1 to add, 0 to change, 1 to destroy.

Changes to Outputs:
  + file_abspath = "/home/jhoon/Documents/study/terraform/ch3/03.start/test.txt"
  + file_id      = (known after apply)

───────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take
exactly these actions if you run "terraform apply" now.
```

이렇게 나온다.

```sh
Changes to Outputs:
  + file_abspath = "/home/jhoon/Documents/study/terraform/ch3/03.start/test.txt"
  + file_id      = (known after apply)
```

이 부분을 보면 알겠지만, `file_abspath` 는 `terraform plan` 에서 계산 가능하지만,  
`file_id` 는 `terraform apply` 이후에 적용되기에 `(known after apply)` 라는 문구가  
적혀있다.

`terraform apply` 이후에는 `file_id` 값이 할당되어서 나온다.
이후에 `terraform output` 으로 `output` 값 출력이 가능하다.

`terraform output file_id`, `terraform output --raw file_id` 로도 출력가능하다.

## 반복문

`list` 혹은 `object` 형식의 타입에서 데이터를 반복적으로 추출하여 처리 가능하다.

### count

> 리소스 또는 모듈 블록에 `count` 값이 정수인 인수가 포함된 경우 선언된  
> 정수 값만큼 리소스나 모듈을 생성한다.

```tf

resource "local_file" "abc" {
  count = 5
  content = "abc"
  filename = "${path.module}/abc.txt"
}

```

`abc.txt` 가 총 5번 생성되지만, `abc.txt` 는 하나만 남게 된다.

```tf

resource "local_file" "abc" {
  count = 5
  content = "abc"
  filename = "${path.module}/abc${count.index}.txt"
}

```

이렇게 하면 해당 숫자가 반복될대마다 해당 `index` 의 값을 할당하여,
파일을 덮어씌우지 않는다.

`length(list type)` 은 해당 `list` 의 `length` 값을 반환한다.
`count = length(var.list변수)` 으로 하면 `length` 값 만큼 생성될것이고,  
`count.index` 를 사용하여 `var.list변수[count.index]` 의 값으로 할당도 가능하다.

`element(object, name)` 방식으로 `object` 의 `값` 을 가져올수도 있다.
`element(var.list변수, count.index)` 이렇게 말이다.

> 서로 참조되는 리소스와 모듈의 반복정의에 대한 공통의 영향을 주는 변수로 관리할 수 있다.

### for_each

> 데이터 형태가 `map` 또는 `set` 이면, 선언되 `key` 값 개수만큼 리소스를  
> 생성하게 된다.

```tf

resource "local_file" "abc" {
  for_each = {
    a = "content a"
    b = "content b"
  }
  content = each.value
  filename = "${path.module}/${each.key}.txt"
}

```

- **`each.key`**: map 의 key
- **`each.name`**: map 의 value

이름 다음처럼도 가능하다

```tf

variable "names" {
  type: map
  default = {
    a = "content a"
    b = "content b"
  }
}

resource "local_file" "abc" {
  for_each = var.names
  content = each.value
  filename = "${path.module}/ab-${each.key}.txt"
}

```

굉장히 편리하게 사용가능하다

```tf

variable "names" {
  default = {
    a = "content a"
    b = "content b"
    c = "content c"
  }
}

resource "local_file" "abc" {
  for_each = var.names
  content = each.value
  filename = "${path.module}/abc-${each.key}.txt"
}

resource "local_file" "def" {
  # local_file.abc 의 값은 local_file.abc 내부의 for_each = var.names 로 인해
  # 만들어진 값이다.
  # 다음과 같다.
  # [
  #   {
  #     "key": "a",
  #     "value": "content a"
  #   },
  #   {
  #     "key": "b",
  #     "value": "content b"
  #   },
  #   {
  #     "key": "c",
  #     "value": "content c"
  #   }
  # ]

  for_each = local_file.abc
  content = each.value.content
  filename = "${path.module}/def-${each.key}.txt"
}


```

여기서 보면 뭔가 문법이 이상하게 작동한다.
이부분은 책에서 따로 설명하지 않는데, 왜 설명안해주는지 모르겠다.

`each.value.content` 로 접근하는 부분과 `each.key` 가 굉장히 이상했다.
생각해보자.

```sh

# local_file.abc 값
[
  {
    "key": "a",
    "value": "content a"
  },
  {
    "key": "b",
    "value": "content b"
  },
  {
    "key": "c",
    "value": "content c"
  }
]

```

이렇게 값이 출력된다.

사용하면서 문법적으로 이상했다.
`each.key` 로 접근하면 해당 배열의 `key` 값이므로 `index` 이어야 한다.
그런데 결과 값으로 `a`, `b`, `c` 로 잘 출력되는것을 볼 수 있다.
그럼 `each.key` 는 현재 객체의 `key` 를 가져온다고 유추할 수 있다.

하지만, `each.value.content` 를 사용하고 있다.
지금 배열의 구문에서 `each.value` 가 현재 객체값을 참조하니  
`each.key` 가 `a` 면 값으로 `content a` 로 나와야 한다.

하지만, 그렇게 접근하면 나오지 않는다.  
`each.value.content` 로 접근해야 `content a` 값이 나온다.

이거 갑자기 존재하지 않은 `content` 가 있으며, `each.key` 의 접근 방식과  
`each.value` 로 접근하는 방식이 상이하다

이부분때문에 잠깐동안 계속 문법만 쳐다보고 있었다.
지금 현재 알게된 부분은 다음과 같다.

`each.value` 는 배열 요소의 key 와 value 를 가진 객체자체이다
`each.key` 는 `each.value.key` 를 참조한다.

그래서 `each.value.value` 형식으로 값에 접근하거나, `each.value.content` 방식으로  
값에 접근해야 한다.

> 이부분은 문법적으로 개선해야할 부분인거 같다.  
> 문법이 순간 혼란스러웠다.

### for

`for` 문을 사용할 수 있다.

```tf

variable "names" {
  default = ["a", "b", "c"]
}

resource "local_file" "abc" {
  content = [jsonencode(for v in var.names: upper(v))] # ["A", "B", "C"]
  filename = "${path.module}/abc.txt"
}

```

```tf
output "with_object" {
  value = {for v in var.names: v => upper(v)}
}

output "with_filter" {
  value = [for v in var.names: upper(v) if v !== 'a']
}
```

이러한 방식으로도 작성 가능하다.

```tf

variabel "members" {
  type = map(object({
    role = string
  }))
  default = {
    ab = { role = "member", group = "dev" }
    cd = { role = "admin", group = "dev" }
    ef = { role = "member", group = "ops" }
  }
}

output "group" {
  value = {
    for name, user in var.members: user.role => name...
  }
}

```

```sh

group = {
  "adming" = [
    "cd"
  ],
  "memeber" = [
    "ab",
    "ef"
  ]
}

```

이렇게 작성된다.
`name...` 부분으로 작성하는데, 해당 `name` 을 배열로 넣어서 처리하는것을  
볼수 있다.

### dynamic

리소스 내의 구성 블록을 다중으로 작성해야 하는 경우에 사용가능하다

```tf

resource "provider_resource" "name" {
  name = "some_resource"
  dynamic "some_setting" {
    for_each = {
      a_key = a_value
      b_key = a_value
      c_key = c_vclue
      d_key = d_value
    }

    content {
      key = some_setting.value
    }
  }
}

```

이는 밑에와 같다.

```tf

resource "provider_resource" "name" {
  name = "some_resource"

  some_setting {
    key = a_value
  }
  some_setting {
    key = b_value
  }
  some_setting {
    key = c_value
  }
  some_setting {
    key = d_value
  }
}

```

## 조건식

조건식은 `3항 연산자` 로 처리한다.

```tf
# <조건 정의> ? <ture 일 경우> : <false 일 경우>
```

> 테라폼 실행시 조건 비교를 위해 형태를 추론하여 자동으로 변환하는데,  
> 이때 협업하는 작업자 사이에 의미 전달이 명확하지 않아 혼란을 겪을수 있으므로  
> 명확한 형태로 작성하기를 권장한다.

자바스크립트에서 타입 추론하는 형태와 비슷한듯하다.
그러므로 `명확한 타입` 을 제공하라고 하는것 같다.

[Conditional Expressions](https://developer.hashicorp.com/terraform/language/expressions/conditionals) 에서 확인가능하다.

## 함수

함수는 사용자 정의 함수는 지원하지 않으며, 내장된 함수만 사용가능하다.
함수의 종류는 `Numberic Functions` `String Functions`, `Collection Functions`,  
`Encoding Functions`, `Filesystem Functions`, `Date and Time Functions`, `Hash and Crypto Functions`, `IP Network Functions`, `Type Conversion Functions` 가 있다.

종류가 많아 [Function Overview](https://developer.hashicorp.com/terraform/language/functions) 의 목록에서 보도록 하자.

## 프로비저너

`프로바이더` 로 실행되지 않는 커맨드와 파일 복사 같은 역할을 수행한다.

> 테라폼의 상태파일과 동기화 되지 않으므로, 프로비저닝에 대한 결과가 항상  
> 같다고 보장 할 수 없다. 따라서 프로비저너 사용을 최소화하는 것이 좋다

여럭 종류가 존재하며, [File Provisioner](ahttps://developer.hashicorp.com/terraform/language/resources/provisioners/file), [Local-exec Provisioner](https://developer.hashicorp.com/terraform/language/resources/provisioners/local-exec), [Remote-exec Provisioner](https://developer.hashicorp.com/terraform/language/resources/provisioners/remote-exec) 에서 볼 수 있다.

## null_resource

프로바이더들이 서로가 서로를 참조하여 실행해야 하는 상황이 생긴다.

자바스크립트로 말하자면, `circular dependency` 를 말하는듯 하다.

> 물론 `nodejs` 차체에서 `circular dependency` 발생시 자체적으로 끊어서  
> 처리하지만, 자체참조 형식으로 순환되는것은 맞다.

`terraform` 에서도 `provider` 가 서로를 참조하는 순간 `circular dependency` 로  
무한 참조가 이루어지는 듯하다.

이럴경우 `terraform` 에서는 `error` 를 내뿜는다.
이때, `null_resource` 가 이러한 무한 참조를 해결해준다.

`null_resource` 는 `아무 작업도 수행하지 않는 resource` 이다.
`resource` 는 해당 `resource` 의 `provider` 를 받고 해당 `provider` 를 사용하여  
프로비저닝을 수행한다.

하지만, `null_resource` 는 그 어떠한 `provider` 도 받지 않으므로,  
수행할 `resource` 는 없다.

이러한 부분을 이용하면, 순환참조되는 현상을 해결할수 있다.

`null_resource` 는 순환참조되는 부분을 빼서 실행하는 `resource` 로 처리를 할때  
유용하다.

```tf

resource "aws_instance" "foo" {
  ami = "<AMI ID>"
  instance_type = "<AWS Instance TYPE>"
  private_ip = "<AWS Pivate IP>"
  subnet_id = aws_subnet.tf_test_subnet.id

  provisioner "remote-exec" {
    # aws_eip.bar 에서 생성된 public_ip 를 echo 한다
    inline = [
      "echo ${aws_eip.bar.public_ip}
    ]
  }
}

resource "aws_eip" "bar" {
  vpc = true

  # aws_instance.foo 의 생성된 instance id
  instance = aws_instance.foo.id
  accosicate_with_private_ip = "<AWS Private IP>"
  depends_on = [aws_internet_gatewat.gw]
}

```

위를 보면 `aws_instance.foo` 는 `provisioner` 에서 `aws_eip.bar.public_ip` 를  
참조하고, `aws_eip.bar` 는 `aws_instance.foo.id` 를 참조한다.

`circular dependency` 가 일어나고 있다
이를 해결하기 위해서는 중간 다리 역할인 `null_resource` 를 사용한다.

```sh
resource "aws_instance" "foo" {
  ami = "<AMI ID>"
  instance_type = "<AWS Instance TYPE>"
  private_ip = "<AWS Pivate IP>"
  subnet_id = aws_subnet.tf_test_subnet.id

}

resource "aws_eip" "bar" {
  vpc = true

  # aws_instance.foo 의 생성된 instance id
  instance = aws_instance.foo.id
  accosicate_with_private_ip = "<AWS Private IP>"
  depends_on = [aws_internet_gatewat.gw]
}

resource "null_resource" "trigger when foo" {
  # 트리거 조건을 `MAP` 형식으로 할당
  # 아래는 `aws.instance.bar.id` 가 변경될때마다 트리거 된다
  trigger = {
    ec2_id = aws.instance.foo.id
  }
  # circuler dependency 가 발생하는 `aws_instance.foo.provisioner` 를
  # `null_resource` 에서 실행
  provisioner "remote-exec" {
    # aws_eip.bar 에서 생성된 public_ip 를 echo 한다
    inline = [
      "echo ${aws_eip.bar.public_ip}
    ]
  }
}

```

이렇게 실행하면 처리 가능하다.
최근버전에서는 `null_resource` 사용방법이 코드가 복잡해지는 경향이 있는지,  
새로운 방법으로 `terraform_data` 라는 새로운 방식을 제공한다.

책에서는 일단 `null_resource` 를 사용하는 방식으로 알려준다고 하니,
`null_resource` 를 사용해보고 `terraform_data` 로 옮겨도 괜찮을 듯 하다.

`null_resource` 보다 더 간단한 문법을 제공한다.

## Moved block

테라폼은 `resource` 의 이름이 변경되면, 기존 리소스는 삭제되고 새로운 리소스가  
생성된다. `terraform resource` 의 이름을 변경해야 하는 상황이 생기는데,  
그때 사용되는 `block` 이 `moved` 블록이다.

> 리소스의 이름은 변경되지만 이미 테라폼으로 프로비저닝 환경을 그대로 유지하고자  
> 하는 경우 `v1.1` 부터 `moved` 블록을 사용할 수 있다.

[Move your resources with the moved configuration block](https://developer.hashicorp.com/terraform/tutorials/configuration-language/move-config#move-your-resources-with-the-moved-configuration-block) 에서 확인하자

## 환경변수

> 테라폼은 환경 변수를 통해 실행방식과 출력 내용에 대한 옵션을 조절할 수 있다.

### TF_LOG

테라폼의 `stderr` 로그에 대한 레벨을 정의

- **TF_LOG**: 로깅 레벨 지정 및 해제  
  trace, debug, info, warn, error, off 설정

- **TF_LOG_PATH**: 로그 출력 파일 위치 지정
- **TF_LOG_CORE**: TF_LOG 와 별도인 테라폼 자체 코어에 대한 로깅 레벨 지정 또는 해제
- **TF_LOG_PROVIDER**: TF_LOG 와 별도로 테라폼에서 사용하는 프로파이더에 대한 로깅 레벨 또는 지정해제

### TF_INPUT

`0` 또는 `false` 로 설정하면 입력동작을 수행하지 않는다.

- `--input=false` 을 추가한것과 동일

### TF_VAR_name

`TF_VAR_<변수 이름>` 을 사용하여 `variable` 에 값을 줄수 있다.
사용하지 않으면 `default` 로 사용된다.

### TF_CLI_ARGS / TF_CLI_ARGS_subcommend

```sh
$> TF_CLI_ARGS="--input=false" terraform apply --auto-approve
```

이렇게 하면 `terraform apply --input=false --auto-approve` 와 같다.

```sh
$> TF_CLI_ARGS_apply="--input=false"
$> terrafrom apply --auto-approve
```

이렇게 하면 `terraform` 에서 `apply` 시에만 `--input=false` 가 작동한다.
`terraform apply --input=false --auto-approve` 와 같다.

### TF_DATA_DIR

`State` 저장 백엔드 설정과 같은 작업 디렉터리별 데이터를 보관하는 위치를 지정한다.
기본값은 `.terraform` 이다. 이 위치를 대체해서 사용가능하다.
