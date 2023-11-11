# State

> 테라폼은 `Stateful` 어플리케이션이다.  
> `provisioning` 결과에 따른 `State` 를 저장하고, `provisioning` 한 모든 내용을  
> 저장한 상태로 추적한다. 로컬 실행 환경에서는 `terrafrom.tfstate` 파일에  
> `JSON` 형태로 저장되고, `team` 이나 조직에서의 공동관리를 위해 `remote registry` 에  
> 저장해 공유하는 방식을 활용한다.

## State 의 목적과 의미

- State 에는 테라폼 구성과 실제를 동기화하고 각 리소스에 고유한 아이디로 매핑
- 리소스 종속성과 같은 메타데이터를 저장하고 추적
- 테라폼 구성으로 프로비저닝된 결과를 캐싱하는 역할을 수행

책에서 `random_password` 를 사용하여 프로바이더 생성한후 `terraform.tfstate` 를  
보여주는 예시가 있는데, 설정내용과 맞지 않다.

순간 내가 잘못 이해하고 있는줄 알았다.

`override_special = "!#$%"` 가 되어있는데, 그 결과값에서 `"!#$%"` 가 아닌 다른  
`special character` 가 포함되어있다.
`result` 에 `!#$%` 의 `special character` 만 포함되어야 하는게 맞다...

여하튼, 그 결과값이다.

```tf
{
  "version": 4,
  "terraform_version": "1.6.3",
  "serial": 4,
  "lineage": "603f6d2f-8210-5c10-41cc-cfe4d42489af",
  "outputs": {},
  "resources": [
    {
      "mode": "managed",
      "type": "random_password",
      "name": "pass",
      "provider": "provider[\"registry.terraform.io/hashicorp/random\"]",
      "instances": [
        {
          "schema_version": 3,
          "attributes": {
            "bcrypt_hash": "$2a$10$vFQK3wSN8YTtXHSQaPhit.RG9asHgWvLk2yfgbbT2b3.6CV03W0Fa",
            "id": "none",
            "keepers": null,
            "length": 16,
            "lower": true,
            "min_lower": 0,
            "min_numeric": 0,
            "min_special": 3,
            "min_upper": 0,
            "number": true,
            "numeric": true,
            "override_special": "!#$%",
            "result": "%u%p$PQ$vViouzko",
            "special": true,
            "upper": true
          },
          "sensitive_attributes": []
        }
      ]
    }
  ],
  "check_results": null
}

```

> `type` 과 `name` 으로 고유한 리소스를 분류하며, 해당 리소스의 속성과 인수를  
> 구성과 비교해 대상 리소스를 생성, 수정, 삭제한다.

`terraform plan` 시에 `state` 값과 실제 존재하는 `resource` 생성 대상과 비교한다.
이러한 과정을 `refresh` 라고 한다.

`terraform plan` 은 암묵적으로 `refersh` 가 작동된다.
이러한 `refresh` 는 끌수도 있는데 `--refresh=false` 를 통해 처리 가능하다.

그러면, 실제 존재하는 `resource` 대상과 비교를 하지 않고 적용한다.

상태관리는 간단하다.

- 코드구성에 있지만, state 및 실제 리소스는 없는경우 --> 새로생성
- 코드구성에 있고, state 가 존재하지만, 실제 리소스는 없는경우 --> 새로생성
- 코드구성, state 및 실제 리소스가 있는경우 --> 동작없음
- 코드구성에 없지만, state 및 실제 리소스는 있는경우 --> 삭제
- 코드구성, state가 없고, 실제 리소스는 있는경우 --> 동작없음

이부분은 매우 명확하게 어떻게 동작하는지 알 수 있다.

## 워크 스페이스

> `state` 를 관리하는 논리적인 가상 공간을 워크스페이스 라고 한다.
> 테라폼 구성 파일은 동일하지만 작업자는 서로 다른 `state` 를 갖는 대상을  
> 프로비저닝할 수 있다.
>
> 워크스페이스는 기본 `default` 로 정의된다. 로컬 작업의 환경의 워크스페이스를
> 관리하기 위한 `CLI` 명령으로 `workspace` 가 있다.

```sh
$> terraform workspace list
* default
```

마치 `VCS` 를 사용하는것 처럼 사용가능한듯 하다.

```sh
$> terraform workspace new myworkspace1

Created and switched to workspace "myworkspace1"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.

$> terraform workspace list

default
* myworkspace1

$> terraform workspace show
myworkspace1
```

이렇게 나온다.
새로운 `workspace` 인 `myworkspace1` 을 생성했다.

`terraform apply` 로 적용하면  
`terraform.tfstate.d/myworkspace1/terraform.tfstate` 가 생성된다.

> `myworkspace1` 에 생성된 `terraform.tfstate` 는 기존 인프라에 영향을  
> 주지 않으면서 간편하게 테라폼 프로비저닝을 테스트하고 확인 가능하다.

`terraform destory --state` 는 지정된 `state`를 삭제한다.

```sh

$> terraform destroy --state=terraform.tfstate.d/myworkspace1/terraform.tfstate
local_file.web: Refreshing state... [id=6471ee8cb090ef960e94058cc804bb8325aa206c]

```

이렇게 해당 `workspace` 의 `state` 를 삭제할수 있으며,  
`workspace` 자체를 삭제하려면 다음처럼 한다.

```sh

$> terraform workspace delete <workspace name>

```
