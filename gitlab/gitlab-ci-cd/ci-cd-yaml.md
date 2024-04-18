# CI/CD YAML 구문 참조

{% embed url="https://gitlab-docs.infograb.net/ee/ci/yaml/" %}
번역본
{% endembed %}

{% embed url="https://docs.gitlab.com/ee/ci/yaml/" %}
원문
{% endembed %}

**`include:template`**

[`.gitlab-ci.yml` 템플릿](https://gitlab.com/gitlab-org/gitlab/-/tree/master/lib/gitlab/ci/templates)을 포함하기 위해 `include:template`를 사용하세요.

**키워드 유형**: 전역 키워드.

**가능한 입력**:

[CI/CD 템플릿](https://gitlab-docs.infograb.net/ee/ci/examples/index.html#cicd-templates):

* 모든 템플릿은 [`lib/gitlab/ci/templates`](https://gitlab.com/gitlab-org/gitlab/-/tree/master/lib/gitlab/ci/templates)에서 볼 수 있습니다. `include:template`와 함께 사용하기 위해 설계된 템플릿은 모두가 아니므로 사용하기 전에 템플릿 설명을 확인하세요.
* [특정 CI/CD 변수](https://gitlab-docs.infograb.net/ee/ci/yaml/includes.html#use-variables-with-include)를 사용할 수 있습니다.

**`include:template`의 예시**:

```
# GitLab 템플릿 컬렉션에서 가져온 파일
include:
- template: Auto-DevOps.gitlab-ci.yml
```

여러 `include:template` 파일:

```
include:
- template: Android-Fastlane.gitlab-ci.yml
- template: Auto-DevOps.gitlab-ci.yml
```

**추가 정보**:

* 모든 [중첩된 포함](https://gitlab-docs.infograb.net/ee/ci/yaml/includes.html#use-nested-includes)은 공개 사용자로서 컨텍스트 없이 실행되므로, 공개 프로젝트 또는 템플릿만 포함할 수 있습니다. 중첩된 포함의 `include` 섹션에서 변수를 사용할 수 없습니다.

***

#### `after_script`

`after_script`를 사용하여 작업의 `script` 섹션 이후에 실행되는 명령의 배열을 정의합니다. 이는 `script_failure` 실패 유형이 있는 실패 작업도 포함합니다. `after_script` 명령은 [다른 실패 유형](https://gitlab-docs.infograb.net/ee/ci/yaml/#retrywhen) 후에는 실행되지 않습니다.

**키워드 유형**: 작업 키워드. 작업의 일부로만 사용할 수 있습니다. 또는 [`default` 섹션](https://gitlab-docs.infograb.net/ee/ci/yaml/#default)에서 사용할 수 있습니다.

**가능한 입력값**: 아래를 포함하는 배열:

* 한 줄 명령어.
* 여러 줄로 [나누어진](https://gitlab-docs.infograb.net/ee/ci/yaml/script.html#split-long-commands) 긴 명령어.
* [YAML 앵커](https://gitlab-docs.infograb.net/ee/ci/yaml/yaml\_optimization.html#yaml-anchors-for-scripts).

CI/CD 변수 [지원됨](https://gitlab-docs.infograb.net/ee/ci/variables/where\_variables\_can\_be\_used.html#gitlab-ciyml-file).

`after_script`의 예시:

```
job:
  script:
    - echo "예시 스크립트 섹션입니다."
  after_script:
    - echo "`script` 섹션이 완료된 후에 이 명령을 실행하세요."
```

**추가 정보**:

`after_script`에 지정한 스크립트는 `before_script` 또는 `script` 명령에서 수행한 변경사항과 독립된 새 쉘에서 실행됩니다. 결과적으로 다음과 같은 특징을 가지게 됩니다:

* 현재 작업 디렉터리는 기본값으로 설정됩니다 ([런너가 Git 요청을 처리하는 방식을 정의하는 변수](https://gitlab-docs.infograb.net/ee/ci/runners/configure\_runners.html#configure-runner-behavior-with-variables)에 따라).
* `before_script` 또는 `script` 스크립트에서 수행한 변경사항의 접근 권한이 없습니다.
  * `script` 스크립트에서 내보낸 명령 별칭 및 변수.
  * `before_script` 또는 `script` 스크립트에서 설치한 소프트웨어와 같은 작업 영역 외의 변경사항 (런너 실행자에 따라 달라집니다).
* 별도의 타임아웃이 적용됩니다. GitLab Runner 16.4 및 이후에는 기본값이 5분이며, [`RUNNER_AFTER_SCRIPT_TIMEOUT`](https://gitlab-docs.infograb.net/ee/ci/runners/configure\_runners.html#set-script-and-after\_script-timeouts) 변수로 구성할 수 있습니다. GitLab 16.3 및 이전 버전에서는 타임아웃이 하드코딩되어 5분으로 설정됩니다.
* 작업의 종료 코드에 영향을 주지 않습니다. `script` 섹션이 성공하고 `after_script` 명령이 타임아웃되거나 실패하더라도 작업은 코드 `0` (`작업 성공`)으로 종료됩니다.

작업이 시간 초과되거나 취소된 경우 `after_script` 명령은 실행되지 않습니다. [타임아웃된 또는 취소된 작업에 `after_script` 명령을 실행하도록 지원하기 위한 이슈가 있습니다](https://gitlab.com/gitlab-org/gitlab/-/issues/15603).

**관련 주제**:

* `default`와 함께 [`after_script` 사용](https://gitlab-docs.infograb.net/ee/ci/yaml/script.html#set-a-default-before\_script-or-after\_script-for-all-jobs) 모든 작업 이후에 실행할 기본 명령어 배열을 정의합니다.
* [0이 아닌 종료 코드 무시](https://gitlab-docs.infograb.net/ee/ci/yaml/script.html#ignore-non-zero-exit-codes).
* `after_script`와 함께 [색 코드 사용](https://gitlab-docs.infograb.net/ee/ci/yaml/script.html#add-color-codes-to-script-output) 작업 로그를 검토하기 쉽게 만듭니다.
* [사용자 정의 축소 가능한 섹션 만들기](https://gitlab-docs.infograb.net/ee/ci/jobs/index.html#custom-collapsible-sections) 작업 로그 출력을 단순화합니다.

***

#### `before_script`

`before_script`을 사용하여 각 작업의 `script` 명령어보다는 실행되지만, [artifacts](https://gitlab-docs.infograb.net/ee/ci/yaml/#artifacts)가 복원된 후에 실행되어야 하는 명령어 배열을 정의합니다.

**키워드 유형**: Job 키워드. 작업의 일부로만 사용하거나 [`default` section](https://gitlab-docs.infograb.net/ee/ci/yaml/#default)에서만 사용할 수 있습니다.

**가능한 입력**: 다음을 포함하는 배열:

* Single line commands.
* [여러 줄에 걸쳐 분할된](https://gitlab-docs.infograb.net/ee/ci/yaml/script.html#split-long-commands) 긴 명령어.
* [YAML 앵커](https://gitlab-docs.infograb.net/ee/ci/yaml/yaml\_optimization.html#yaml-anchors-for-scripts).

CI/CD 변수 [지원됨](https://gitlab-docs.infograb.net/ee/ci/variables/where\_variables\_can\_be\_used.html#gitlab-ciyml-file).

**`before_script` 예시**:

```
job:
  before_script:
    - echo "스크립트 명령어 이전에 이 명령어를 실행합니다."
  script:
    - echo "이 명령어는 작업의 'before_script' 명령어 후에 실행됩니다."
```

**추가 정보**:

* `before_script`에서 지정한 스크립트는 주요 [`script`](https://gitlab-docs.infograb.net/ee/ci/yaml/#script)에서 지정한 스크립트와 연결되어 단일 셸에서 함께 실행됩니다.
* 최상위 수준의 `before_script`을 사용하되 `default` section에는 사용하지 말아야 합니다. [폐기됨](https://gitlab-docs.infograb.net/ee/ci/yaml/#globally-defined-image-services-cache-before\_script-after\_script).

**관련 주제**:

* \[default`와 함께` before\_script `사용](script.md#set-a-default-before_script-or-after_script-for-all-jobs)하여 모든 작업의` script\` 명령어 전에 실행되어야 하는 기본 명령어 배열을 정의합니다.
* [0이 아닌 종료 코드 무시](https://gitlab-docs.infograb.net/ee/ci/yaml/script.html#ignore-non-zero-exit-codes).
* [before\_script\`에 색상 코드 사용](https://gitlab-docs.infograb.net/ee/ci/yaml/script.html#add-color-codes-to-script-output)하여 작업 로그를 쉽게 검토할 수 있습니다.
* [사용자 지정 접기 가능한 섹션 만들기](https://gitlab-docs.infograb.net/ee/ci/jobs/index.html#custom-collapsible-sections)하여 작업 로그 출력을 단순화합니다.

***

#### `retry`

`retry`를 사용하여 작업이 실패할 경우 재시도되는 횟수를 구성하세요. 정의되지 않으면 기본값은 `0`이며 작업은 재시도되지 않습니다.

작업이 실패하면 해당 작업은 성공 또는 최대 재시도 횟수에 도달할 때까지 2회 더 처리됩니다.

기본적으로 모든 실패 유형은 작업을 재시도하게 합니다. [`retry:when`](https://gitlab-docs.infograb.net/ee/ci/yaml/#retrywhen) 또는 [`retry:exit_codes`](https://gitlab-docs.infograb.net/ee/ci/yaml/#retryexit\_codes)을 사용하여 어떤 실패를 재시도할지 선택하세요.

**Keyword type**: Job keyword. 작업 또는 [`default` 섹션](https://gitlab-docs.infograb.net/ee/ci/yaml/#default) 일부로만 사용할 수 있습니다.

**가능한 입력값**:

* `0` (기본값), `1`, 또는 `2`.

**`retry`의 예시**:

```
test:
  script: rspec
  retry: 2

test_advanced:
  script:
    - echo "종료 코드가 137인 스크립트 실행."
    - exit 137
  retry:
    max: 2
    when: runner_system_failure
    exit_codes: 137
```

`test_advanced`는 종료 코드가 `137`이거나 실행중인 시스템에 장애가 발생할 경우 최대 2회까지 재시도됩니다.

**`retry:when`**

`retry:when`을 `retry:max`와 함께 사용하여 특정 실패 사례에 대해 작업을 재시도하세요. `retry:max`는 [`retry`](https://gitlab-docs.infograb.net/ee/ci/yaml/#retry)와 같이 최대 재시도 횟수이며 `0`, `1`, 또는 `2`가 될 수 있습니다.

**Keyword type**: Job keyword. 작업 또는 [`default` 섹션](https://gitlab-docs.infograb.net/ee/ci/yaml/#default) 일부로만 사용할 수 있습니다.

**가능한 입력값**:

* 단일 실패 유형 또는 하나 이상의 실패 유형 배열:
* `always`: 모든 실패시 재시도 (기본값).
* `unknown_failure`: 실패 이유가 알려지지 않았을 때 재시도.
* `script_failure`: 다음 경우에 재시도:
  * 스크립트 실행 실패시
  * 러너가 Docker 이미지를 가져오지 못했을 때. `docker`, `docker+machine`, `kubernetes` [executors](https://docs.gitlab.com/runner/executors/).
* `api_failure`: API 실패시 재시도.
* `stuck_or_timeout_failure`: 작업이 멈추거나 시간이 초과되었을 때 재시도.
* `runner_system_failure`: 러너 시스템 장애시 재시도 (예: 작업 설정 실패).
* `runner_unsupported`: 러너가 지원되지 않을 경우 재시도.
* `stale_schedule`: 지연된 작업을 실행할 수 없을 경우 재시도.
* `job_execution_timeout`: 작업에 설정된 최대 실행 시간을 초과했을 때 재시도.
* `archived_failure`: 작업이 보관되어 실행할 수 없을 경우 재시도.
* `unmet_prerequisites`: 작업이 선행 작업을 완료하지 못했을 때 재시도.
* `scheduler_failure`: 스케줄러가 작업을 러너에 할당하지 못했을 때 재시도.
* `data_integrity_failure`: 알 수 없는 작업 문제가 있을 경우 재시도.

**`retry:when`의 예시** (단일 실패 유형):

```
test:
  script: rspec
  retry:
    max: 2
    when: runner_system_failure
```

러너 시스템 장애가 아닌 경우에는 작업이 재시도되지 않습니다.

**`retry:when`의 예시** (여러 실패 유형 배열):

```
test:
  script: rspec
  retry:
    max: 2
    when:
      - runner_system_failure
      - stuck_or_timeout_failure
```

**`retry:exit_codes`**

> * [GitLab 16.10에서 도입](https://gitlab.com/gitlab-org/gitlab/-/issues/430037)됨 [플래그](https://gitlab-docs.infograb.net/ee/administration/feature\_flags.html) `ci_retry_on_exit_codes`와 함께. 기본적으로 비활성화.

플래그: 자체 호스팅 GitLab에서는 기본적으로 이 기능을 사용할 수 없습니다. 사용하려면 관리자가 [플래그 활성화](https://gitlab-docs.infograb.net/ee/administration/feature\_flags.html)할 수 있습니다. `ci_retry_on_exit_codes`라는 플래그를 사용하세요.

`retry:exit_codes`를 `retry:max`와 함께 사용하여 특정 실패 사례에 대해 작업을 재시도하세요. `retry:max`는 [`retry`](https://gitlab-docs.infograb.net/ee/ci/yaml/#retry)와 같이 최대 재시도 횟수이며 `0`, `1`, 또는 `2`가 될 수 있습니다.

**Keyword type**: Job keyword. 작업 또는 [`default` 섹션](https://gitlab-docs.infograb.net/ee/ci/yaml/#default) 일부로만 사용할 수 있습니다.

**가능한 입력값**:

* 단일 종료 코드.
* 종료 코드 배열.

**`retry:exit_codes`의 예시**:

```
test_job_1:
  script:
    - echo "종료 코드가 1인 스크립트 실행. 이 작업은 재시도되지 않습니다."
    - exit 1
  retry:
    max: 2
    exit_codes: 137

test_job_2:
  script:
    - echo "종료 코드가 137인 스크립트 실행. 이 작업은 재시도될 것입니다."
    - exit 137
  retry:
    max: 1
    exit_codes:
      - 255
      - 137
```

**관련 주제**:

변수를 사용하여 작업 실행의 [특정 단계에 대한 재시도 시도 횟수](https://gitlab-docs.infograb.net/ee/ci/runners/configure\_runners.html#job-stages-attempts)를 지정할 수 있습니다.

***

#### `rules`

> * [GitLab 12.3](https://gitlab.com/gitlab-org/gitlab/-/issues/27863)에서 소개되었습니다.

`rules`를 사용하여 파이프라인에서 작업을 포함하거나 제외합니다.

`rules`는 파이프라인이 생성될 때 평가되며, 평가는 _순서대로_ 진행됩니다. 일치하는 항목이 발견되면, 작업은 구성에 따라 파이프라인에 포함되거나 제외됩니다.

작업 스크립트에서 생성된 dotenv 변수를 `rules`에서 사용할 수 없습니다. 이는 `rules`가 작업 실행 전에 평가되기 때문입니다.

`rules`는 [`only/except`](https://gitlab-docs.infograb.net/ee/ci/yaml/#only--except)를 대체하고, 동일한 작업에서 함께 사용할 수 없습니다. 한 작업에서 두 키워드를 구성하면 GitLab에서 `key may not be used with rules` 오류가 반환됩니다.

`rules`는 다음과 같이 정의된 규칙 배열을 허용합니다:

* `if`
* `changes`
* `exists`
* `allow_failure`
* `variables`
* `when`

여러 키워드를 함께 결합하여 [복잡한 규칙](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#complex-rules)을 만들 수 있습니다.

작업은 파이프라인에 추가됩니다:

* `if`, `changes`, 또는 `exists` 규칙이 일치하고, 또한 `when: on_success` (기본값), `when: delayed`, 또는 `when: always`를 갖는 경우.
* 특정 `when: on_success`, `when: delayed`, 또는 `when: always`에 도달한 경우.

작업은 파이프라인에 추가되지 않습니다:

* 규칙이 일치하지 않는 경우.
* 규칙이 일치하고 `when: never`를 갖는 경우.

[`!참조` 태그](https://gitlab-docs.infograb.net/ee/ci/yaml/yaml\_optimization.html#reference-tags)를 사용하여 서로 다른 작업에서 [규칙 구성 재사용](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#reuse-rules-in-different-jobs)을 할 수 있습니다.

**`rules:if`**

`rules:if` 절을 사용하여 작업을 파이프라인에 추가할 시기를 지정합니다:

* `if` 문이 true이면, 작업이 파이프라인에 추가됩니다.
* `if` 문이 true이지만 `when: never`와 결합된 경우, 작업이 파이프라인에 추가되지 않습니다.
* 어떤 `if` 문도 true가 아닌 경우, 작업이 파이프라인에 추가되지 않습니다.

`if` 절은 [CI/CD 변수](https://gitlab-docs.infograb.net/ee/ci/variables/index.html) 또는 [사전 정의된 CI/CD 변수](https://gitlab-docs.infograb.net/ee/ci/variables/predefined\_variables.html)의 값에 따라 평가됩니다. [일부 예외](https://gitlab-docs.infograb.net/ee/ci/variables/where\_variables\_can\_be\_used.html#gitlab-ciyml-file)가 있습니다.

**키워드 유형**: 작업별 및 파이프라인별. 작업의 동작을 구성하기 위해 작업 부분으로 사용하거나, [`workflow`](https://gitlab-docs.infograb.net/ee/ci/yaml/#workflow)와 함께 사용하여 파이프라인 동작을 구성할 수 있습니다.

**가능한 입력**:

* [CI/CD 변수 표현식](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#cicd-variable-expressions).

`rules:if`의 예시:

```
job:
  script: echo "안녕, Rules!"
  rules:
    - if: $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME =~ /^feature/ && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME != $CI_DEFAULT_BRANCH
      when: never
    - if: $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME =~ /^feature/
      when: manual
      allow_failure: true
    - if: $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME
```

**추가 세부 정보**:

* 규칙이 일치하고 정의된 `when`이 없는 경우, 규칙은 작업에 대해 정의된 기본값이 `on_success`인 `when`을 사용합니다.
* GitLab 14.5 이전에는 규칙 당 `when`을 한 번 정의하거나 작업 수준에서 한 번 정의할 수 있었습니다. 이는 모든 규칙에 적용되는 것입니다. 작업 수준의 `when`과 규칙 내의 `when`을 혼합할 수 없습니다.
* GitLab 14.6 이후에는 [작업 수준의 `when`과 규칙 내의 `when`을 혼합](https://gitlab.com/gitlab-org/gitlab/-/issues/219437)할 수 있습니다. `rules`의 `when` 구성이 작업 수준의 `when`을 우선합니다.
* [`script`](https://gitlab-docs.infograb.net/ee/ci/variables/index.html#use-cicd-variables-in-job-scripts) 섹션의 변수와 달리, 규칙 표현식의 변수는 항상`$VARIABLE` 형식으로 작성됩니다.
  * `rules:if`와 `include`를 함께 사용하여 [조건부로 다른 구성 파일을 포함](https://gitlab-docs.infograb.net/ee/ci/yaml/includes.html#use-rules-with-include)할 수 있습니다.
* `=~` 및 `!~` 표현식의 오른쪽에 있는 CI/CD 변수는 [정규 표현식으로 평가](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#store-the-regex-pattern-in-a-variable)됩니다.

**관련 주제**:

* `rules`를 위한 [일반적인 `if` 표현식](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#common-if-clauses-for-rules).
* [중복 파이프라인 피하기](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#avoid-duplicate-pipelines).
* [병합 요청 파이프라인 실행에 `rules` 사용하기](https://gitlab-docs.infograb.net/ee/ci/pipelines/merge\_request\_pipelines.html#use-rules-to-add-jobs).

**`rules:changes`**

`rules:changes`을 사용하여 특정 파일의 변경 사항을 확인하여 파이프라인에 작업을 추가할 때 지정합니다.

경고: `rules: changes`는 **브랜치 파이프라인** 또는 **병합 요청 파이프라인**에서만 사용해야합니다. 다른 파이프라인 유형에서도 `rules: changes`를 사용할 수 있지만, `rules: changes`는 항상 새로운 브랜치 파이프라인이거나 Git `push` 이벤트가 없는 경우에는 항상 참으로 평가됩니다. 태그 파이프라인, 예약된 파이프라인 및 수동 파이프라인과 같은 파이프라인은 Git `push` 이벤트가 연결되어 있지 않습니다. 이러한 경우에는 비교할 브랜치를 지정하기 위해 [`rules: changes: compare_to`](https://gitlab-docs.infograb.net/ee/ci/yaml/#ruleschangescompare\_to)를 사용하십시오.

**키워드 유형**: 작업 키워드. 작업의 일부로만 사용할 수 있습니다.

**가능한 입력**:

특정 수의 포함된 배열: - 파일의 경로. GitLab 13.6 이상에서는 [변수를 포함한 파일 경로](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#variables-in-ruleschanges)를 포함할 수 있습니다. 파일 경로 배열은 또한 [`rules:changes:paths`](https://gitlab-docs.infograb.net/ee/ci/yaml/#ruleschangespaths)에 있을 수 있습니다. - 다음을 위한 와일드카드 경로: - 단일 디렉토리에 대한 경로, 예를 들어 `path/to/directory/*`. - 디렉토리와 해당 하위 디렉토리에 대한 와일드카드 경로, 예를 들어 `path/to/directory/**/*`. - 동일한 확장자 또는 여러 확장자를 가진 모든 파일을 위한 와일드카드 [glob](https://en.wikipedia.org/wiki/Glob\_\(programming\)) 경로, 예를 들어 `*.md` 또는 `path/to/directory/*.{rb,py,sh}`. - 루트 디렉토리의 파일이나 모든 디렉토리에 대한 와일드카드 경로는 이중 인용 부호로 묶여 있습니다. 예를 들어 `"*.json"` 또는 `"**/*.json"`.

**`rules:changes`의 예시**:

```
docker build:
  script: docker build -t my-image:$CI_COMMIT_REF_SLUG .
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      changes:
        - Dockerfile
      when: manual
      allow_failure: true
```

* 파이프라인이 병합 요청 파이프라인인 경우 `Dockerfile`을 변경 사항을 확인하십시오.
* `Dockerfile`에 변경 사항이 있으면 작업을 수동 작업으로 파이프라인에 추가하고, 작업이 트리거되지 않아도 파이프라인이 계속 실행됩니다 (`allow_failure: true`).
* `rules: changes` 섹션 당 최대 50개의 패턴 또는 파일 경로를 정의할 수 있습니다.
* `Dockerfile`에 변경 사항이 없으면 어떤 파이프라인에도 작업을 추가하지 마십시오 (`when: never`와 동일함).
* [`rules:changes:paths`](https://gitlab-docs.infograb.net/ee/ci/yaml/#ruleschangespaths)는 하위 키가 없는 `rules: changes`와 동일합니다.

**추가 세부 정보**: - `rules: changes`는 [`only: changes` 및 `except: changes`](https://gitlab-docs.infograb.net/ee/ci/yaml/#onlychanges--exceptchanges)와 동일한 방식으로 작동합니다. - Glob 패턴은 Ruby의 [`File.fnmatch`](https://docs.ruby-lang.org/en/master/File.html#method-c-fnmatch) 및 [flags](https://docs.ruby-lang.org/en/master/File/Constants.html#module-File::Constants-label-Filename+Globbing+Constants+-28File-3A-3AFNM\_-2A-29)의 해석과 함께 구현됩니다. - [`except:changes`](https://gitlab-docs.infograb.net/ee/ci/yaml/#onlychanges--exceptchanges)와 비슷한 규칙을 구현하려면 `when: never`를 사용할 수 있습니다. - `changes`는 일치하는 파일 중 하나라도 변경되었을 때 `true`로 해결됩니다(`OR` 연산).

**관련 주제**: - [`rules: changes`](https://gitlab-docs.infograb.net/ee/ci/yaml/%22../jobs/job\_troubleshooting.md#jobs-or-pipelines-run-unexpectedly-when-using-changes%22)을 사용할 때 작업이 또는 파이프라인이 예상치 않게 실행될 수 있습니다.

**`rules:changes:paths`**

> * [GitLab 15.2에서 소개](https://gitlab.com/gitlab-org/gitlab/-/merge\_requests/90171).

`rules:changes`를 사용하여 특정 파일이 변경될 때만 작업이 파이프라인에 추가되도록 지정하고, `rules:changes:paths`를 사용하여 파일을 지정합니다.

`rules:changes:paths`는 하위 키가 없는 [`rules:changes`](https://gitlab-docs.infograb.net/ee/ci/yaml/#ruleschanges)를 사용하는 것과 동일합니다. 모든 추가 세부 정보와 관련 주제는 동일합니다.

**키워드 유형**: 작업 키워드. 작업의 일부로만 사용할 수 있습니다.

**가능한 입력**: - 파일 경로의 배열. [파일 경로에 변수를 포함](https://gitlab-docs.infograb.net/ee/ci/jobs/job\_control.html#variables-in-ruleschanges)시킬 수 있습니다.

**`rules:changes:paths`의 예시**:

```
docker-build-1:
  script: docker build -t my-image:$CI_COMMIT_REF_SLUG .
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      changes:
        - Dockerfile

docker-build-2:
  script: docker build -t my-image:$CI_COMMIT_REF_SLUG .
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      changes:
        paths:
          - Dockerfile
```

이 예시에서 두 작업은 동일한 동작을 합니다.

**`rules:changes:compare_to`**

* [15.3에서 도입](https://gitlab.com/gitlab-org/gitlab/-/issues/293645)되었습니다. 기본적으로 사용 가능한 `ci_rules_changes_compare` 플래그와 함께.
* [15.5에서 일반적으로 사용 가능](https://gitlab.com/gitlab-org/gitlab/-/issues/366412)하며 `ci_rules_changes_compare` 플래그가 제거되었습니다.

`rules:changes:compare_to`를 사용하여 `rules:changes:paths`에 나열된 파일의 변경 사항을 비교할 대상 ref를 지정합니다.

**키워드 유형**: 작업 키워드. 작업의 일부로만 사용할 수 있으며 `rules:changes:paths`와 함께 사용되어야 합니다.

**가능한 입력**:

* `main`, `branch1`, 또는 `refs/heads/branch1`과 같은 브랜치 이름.
* `tag1` 또는 `refs/tags/tag1`과 같은 태그 이름.
* `2fg31ga14b`와 같은 커밋 SHA.

**`rules:changes:compare_to`의 예시**:

```
docker build:
  script: docker build -t my-image:$CI_COMMIT_REF_SLUG .
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      changes:
        paths:
          - Dockerfile
        compare_to: 'refs/heads/branch1'
```

이 예에서 `docker build` 작업은 `Dockerfile`이 `refs/heads/branch1`을 기준으로 변경되었고 파이프라인 소스가 병합 요청 이벤트인 경우에만 포함됩니다.

**`rules:exists`**

* [12.4에서 도입](https://gitlab.com/gitlab-org/gitlab/-/issues/24021)되었습니다.
* CI/CD 변수 지원은 [15.6에서 도입](https://gitlab.com/gitlab-org/gitlab/-/issues/283881)되었습니다.

`exists`를 사용하여 저장소에 특정 파일이 존재할 때 작업을 실행합니다.

**키워드 유형**: 작업 키워드. 작업의 일부로만 사용할 수 있습니다.

**가능한 입력**:

* 파일 경로 배열. 경로는 프로젝트 디렉토리(`$CI_PROJECT_DIR`)를 기준으로 상대적이며 직접 외부로 연결할 수 없습니다. 파일 경로에는 glob 패턴과 [CI/CD 변수](https://gitlab-docs.infograb.net/ee/ci/variables/where\_variables\_can\_be\_used.html#gitlab-ciyml-file)를 사용할 수 있습니다.

**`rules:exists`의 예시**:

```
job:
  script: docker build -t my-image:$CI_COMMIT_REF_SLUG .
  rules:
    - exists:
        - Dockerfile
```

`Dockerfile`이 저장소 어디에 있든, `job`은 실행됩니다.

**추가 세부 정보:**

* glob 패턴은 Ruby의 [`File.fnmatch`](https://docs.ruby-lang.org/en/master/File.html#method-c-fnmatch)와 함께 [flags](https://docs.ruby-lang.org/en/master/File/Constants.html#module-File::Constants-label-Filename+Globbing+Constants+-28File-3A-3AFNM\_-2A-29)를 사용하여 해석됩니다(`File::FNM_PATHNAME | File::FNM_DOTMATCH | File::FNM_EXTGLOB`).
* 성능상의 이유로, GitLab은 `exists` 패턴 또는 파일 경로에 대해 최대 10,000번의 확인을 수행합니다. 10,000번째 확인 이후에는 패턴화된 glob은 항상 일치합니다. 다시 말해, `exists` 규칙은 항상 일치한다고 가정합니다. 이는 10,000개 이상의 파일이 있는 프로젝트이거나 `exists` 규칙이 10,000번 이상 확인된 경우에도 동일합니다.
* `rules:exists` 섹션 당 최대 50개의 패턴 또는 파일 경로를 정의할 수 있습니다.
* `exists`는 나열된 파일 중 하나라도 발견되면 `true`로 해석됩니다(`OR` 연산).

**`rules:allow_failure`**

* [12.8에서 도입](https://gitlab.com/gitlab-org/gitlab/-/issues/30235)되었습니다.

`rules`에서 [`allow_failure: true`](https://gitlab-docs.infograb.net/ee/ci/yaml/#allow\_failure)를 사용하여 작업이 실패해도 파이프라인을 중지시키지 않도록 허용합니다.

수동 작업에도 `allow_failure: true`를 사용할 수 있습니다. 파이프라인은 수동 작업의 결과를 기다리지 않고 계속 실행됩니다. 규칙에 따른 `allow_failure: false`는 수동 작업이 실행될 때까지 파이프라인을 기다리게 합니다.

**키워드 유형**: 작업 키워드. 작업의 일부로만 사용할 수 있습니다.

**가능한 입력**:

* `true` 또는 `false`. 정의되지 않은 경우 기본값은 `false`입니다.

**`rules:allow_failure`의 예시**:

```
job:
  script: echo "Hello, Rules!"
  rules:
    - if: $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == $CI_DEFAULT_BRANCH
      when: manual
      allow_failure: true
```

규칙이 일치하는 경우, 작업은 `allow_failure: true`를 가진 수동 작업입니다.

**추가 세부 정보:**

* 규칙 수준의 `rules:allow_failure`는 작업 수준의 [`allow_failure`](https://gitlab-docs.infograb.net/ee/ci/yaml/#allow\_failure)를 재정의하며, 특정 규칙이 작업을 트리거할 때만 적용됩니다.

**`rules:needs`**

> * [GitLab 16.0](https://gitlab.com/gitlab-org/gitlab/-/issues/31581)에서 `introduce_rules_with_needs`이라는 플래그로 소개되었습니다. 기본적으로 비활성화됩니다.
> * [GitLab 16.2](https://gitlab.com/gitlab-org/gitlab/-/issues/408871)에서 일반적으로 사용할 수 있게 되었습니다. 기능 플래그 `introduce_rules_with_needs`가 제거되었습니다.

`needs`를 사용하여 규칙에서 작업의 [`needs`](https://gitlab-docs.infograb.net/ee/ci/yaml/#needs)를 조건에 따라 업데이트합니다. 조건이 규칙과 일치하는 경우 작업의 `needs` 구성은 규칙의 `needs`로 완전히 대체됩니다.

**키워드 유형**: 작업별. 작업의 일부로만 사용할 수 있습니다.

**가능한 입력**:

* 문자열로 된 작업 이름의 배열.
* 작업 이름과 선택적으로 추가 속성이 포함된 해시.
* 특정 조건을 충족하는 경우 작업 needs를 없음으로 설정하려면 빈 배열(`[]`)을 사용합니다.

**`rules:needs` 예시**:

```
build-dev:
  stage: build
  rules:
    - if: $CI_COMMIT_BRANCH != $CI_DEFAULT_BRANCH
  script: echo "기능 브랜치이므로 개발 버전 빌드 중..."

build-prod:
  stage: build
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
  script: echo "기본 브랜치이므로 프로덕션 버전 빌드 중..."

specs:
  stage: test
  needs: ['build-dev']
  rules:
    - if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
      needs: ['build-prod']
    - when: on_success # 다른 경우에도 작업 실행
  script: echo "기본적으로 개발 사양 실행 또는 기본 브랜치일 때 프로덕션 사양 실행..."
```

이 예시에서:

* 파이프라인이 기본 브랜치가 아닌 브랜치에서 실행되면 `specs` 작업은 `build-dev` 작업이 필요합니다(기본 동작).
* 파이프라인이 기본 브랜치에서 실행되고 따라서 규칙이 조건에 일치하면 `specs` 작업은 대신에 `build-prod` 작업이 필요합니다.

**추가 세부 정보**:

* 규칙의 `needs`는 작업 수준에서 정의된 `needs`를 재정의합니다. 재정의된 경우 동작은 [작업 수준 `needs`](https://gitlab-docs.infograb.net/ee/ci/yaml/#needs)과 동일합니다.
* 규칙의 `needs`는 [`artifacts`](https://gitlab-docs.infograb.net/ee/ci/yaml/#needsartifacts) 및 [`optional`](https://gitlab-docs.infograb.net/ee/ci/yaml/#needsoptional)을 허용합니다.

**`rules:variables`**

> * [GitLab 13.7](https://gitlab.com/gitlab-org/gitlab/-/issues/209864)에서 도입되었습니다.
> * [기능 플래그가 제거](https://gitlab.com/gitlab-org/gitlab/-/issues/289803)된 GitLab 13.10에서.

`rules`에서 [`variables`](https://gitlab-docs.infograb.net/ee/ci/yaml/#variables)을 사용하여 특정 조건에 대한 변수를 정의합니다.

**키워드 유형**: 작업별. 작업의 일부로만 사용할 수 있습니다.

**가능한 입력**:

* `VARIABLE-NAME: 값을` 형식으로 된 변수 해시.

**`rules:variables` 예시**:

```
job:
  variables:
    DEPLOY_VARIABLE: "default-deploy"
  rules:
    - if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
      variables:                              # 작업 수준에서 정의된 DEPLOY_VARIABLE을 재정의
        DEPLOY_VARIABLE: "deploy-production"  #합니다.
    - if: $CI_COMMIT_REF_NAME =~ /feature/
      variables:
        IS_A_FEATURE: "true"                  # 새 변수를 정의합니다.
  script:
    - echo "$DEPLOY_VARIABLE을(를) 인자로 사용하여 스크립트 실행"
    - "$IS_A_FEATURE가 있는 경우 다른 스크립트 실행"
```

**`rules:interruptible`**

> * [GitLab 16.10](https://gitlab.com/gitlab-org/gitlab/-/issues/194023)에서 도입되었습니다.

`interruptible`를 사용하여 규칙에서 특정 조건에 대한 작업의 [`interruptible`](https://gitlab-docs.infograb.net/ee/ci/yaml/#interruptible) 값을 업데이트합니다.

**키워드 유형**: 작업별. 작업의 일부로만 사용할 수 있습니다.

**가능한 입력**:

* `true` 또는 `false`.

**`rules:interruptible` 예시**:

```
job:
  script: echo "안녕, 규칙!"
  interruptible: true
  rules:
    - if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
      interruptible: false  # 작업 수준에서 정의된 interruptible을 재정의합니다.
    - when: on_success
```

**추가 세부 정보**:

* 규칙 수준의 `rules:interruptible`은 작업 수준의 [`interruptible`](https://gitlab-docs.infograb.net/ee/ci/yaml/#interruptible)을 재정의하며 특정 규칙이 작업을 트리거할 때만 적용됩니다.

\
