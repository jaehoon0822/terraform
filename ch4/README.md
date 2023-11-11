# 프로바이더

`terraform` 은 `terraform` 바이너리 파일을 시작으로 로컬 환경이나 배포 서버와 같은  
원격 환경에서 원하는 대상을 호출하는 방식으로 실행된다.

이때 그 대상을 `provider` 라 하면, 해당 `provider` 에서 제공하는 `API`를 호출하여  
상호작용 한다.

각 프로바이더는 테라폼이 관리하는 리소스 유형과 데이터 소스를 사용할 수 있도록  
연결한다.

대부분의 프로바이더는 대상 인프라 환경이나, 서비스 환경에 대해 리소스를 관리하므로,  
프로바이더를 구성할때는 대상과의 연결과 인증에 필요한 정보가 제공되어야 한다.

이때 제공되는 리소스는 `Terraform Registry` 를 사용하여 `provider` 를 사용하고,  
관련 문서를 확인할 수 있다.

`Provier` 는 여러 가지가 존재한다.

- **`Offical`**: `Hashicorp` 가 관리한다.
- **`Partner`**: 기술 파트너가 관리한다.
  (`mongodb/mongodbatlas` 는 `mongodb` 파트너가 관리한다.)
- **`Community`**: 개인 및 조직이 관리한다.
  (`DeviaVir/gsuite` 는 개인 및 조직이름이다.)
- **`Archived`**: 유지 보수되지 않는 이전 프로바이더이다.
  (`hashicorp 또는 타 조직`)

## 프로바이더 지정

`terraform block` 에서 `required_providers` 블록에 `provider` 지정이 가능하다
각 프로바이더의 소스 경로가 지정되면, 프로바이더의 고유 접두사가 제공된다.
만약 동일한 접두사를 사용하는 프로바이더가 선언되는 경우 `local name` 을 달리하여  
리소스에서 어떤 프로바이더를 사용하는지 명시적으로 지정할 수 있다.

```ts
terrafrom {
  require_providers {
    // localname
    architect-http = {
      source = "architect-team/http"
      version = "~> 3.0"
    }
    http = {
      source = "hashicorp/http"
    }
    aws-http = {
      source = "terraform-aws-modules/http"
    }
  }
}

data "http" "example" {
  provider = aws-http
  url = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
}

required_headers = {
  Accept = "application/json"
}

```

### 단일 프로바이더의 다중 정의

같은 프로바이더이지만, 여러 프로바이더로 정의하는 경우가 있다.
`AWS` 프로바이더를 사용하는데 `region` 이 다르다면 달리 처리해야 한다.

이때 `provider` 블록을 사용하여 처리한다.

```ts

provider "aws" {
  region = "us-west-1"
}

provider "aws" {
  alias = "seoul"
  region = "ap-northeast-2"
}

resource "aws_instance" "app_server" {
  provider = aws.seoul
  ami = "<AMI ID>"
  instance_type = "<Instance Type>"
}

```

위에 보면 `alias` 를 사용하여 `seoul` 리전을 식별한다.

### 프로바이더 요구사항 정의

`required_providers` 에 사용되는 정의 블록은 다음처럼 구성되어 있다.

```ts

terraform {
  required_providers {
    <provider_local_name> {
      source = [<host address>/]<name_space>/<type>
      version = <version constraint>
    }
  }
}

```

- `host address` 는 생략가능하며, `default` 로 `registry.terraform.io` 이다.
- `name_space` 는 `registry` 내의 프로바이더를 게시하는 조직을 말한다.
- `type` 은 `provider` 에서 관리하는 `platform` 이나 `service name` 이다.  
  접두사와 일치하나 일부 예외가 있을수 있다고 한다.

이렇게 하면 `provider` 가 `terrafom init` 할때 설치된다.  
`required_providers` 에 지정된 프로바이더가 있다면 사용여부 상관없이 무조건  
다운로드 되며, 그 반대로 `required_providers` 에 프로바이더가 없지만,  
프로바이더를 사용하고 있다면 자동으로 다운로드 한다.

코드상에 사용된 프로바이더는 최신버전의 프로바이더로 다운로드한다.

프로바이더 전환시(`AWS` 에서 `GCP` 로) 테라폼 구성 코드와 상태파일에는 기록되므로  
클라우드 리소스 전환을 위해 현황 파악에 도움이 된다고 한다.

> 말이 애매한데, 프로바이더마다 `API` 가 달라 호환은 어렵더라도,  
> `terraform` 특성상, 수작업보다는 편하며, 코드 파악이 수월하다는 말 같다.
>
> 대응 되는 `api` 만 변경하면 더 수월하다는 것 같다.

## 프로바이더 에코시스템

테라폼은 `workflow partner` 와 `infra partner` 로 나뉜다.
`workflow partner` 는 테라폼 실행시 `terraform cloude/enterprise` 와 연계하여  
동작하는 기능을 제공한다.

> 테라폼 구성 관리를 위한 `VCS` 를 제공하는 `gitlab`, `bitbukcet`, `azure devops` 라고 한다.

`provider` 의 경우에는 `infra partner` 에 해당한다고 한다.
`github` 는 `terraform` 으로 `provisioning` 가능한 `infra partner` 라 하며,  
`github` 를 프로비저닝하고 관리 가능하다.
