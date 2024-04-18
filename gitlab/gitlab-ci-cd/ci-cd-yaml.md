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
