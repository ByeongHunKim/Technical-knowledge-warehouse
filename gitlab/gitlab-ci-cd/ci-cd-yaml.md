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
