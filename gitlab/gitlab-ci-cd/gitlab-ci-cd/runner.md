# Runner

Runner는 작업을 실행하는 에이전트입니다. 이 에이전트는 물리적 머신 또는 가상 인스턴스에서 실행될 수 있습니다. `.gitlab-ci.yml` 파일에서 작업을 실행할 때 사용할 컨테이너 이미지를 지정할 수 있습니다. Runner는 이미지를 로드하고 프로젝트를 복제한 다음, 작업을 로컬에서 또는 컨테이너 내에서 실행합니다.

GitLab.com을 사용하는 경우, Linux, Windows 및 macOS용 runners를 이미 사용할 수 있습니다. 또한 원한다면 GitLab.com에서 자체 runners를 등록할 수 있습니다.

GitLab.com을 사용하지 않는 경우 다음을 할 수 있습니다:

* 자체 관리 인스턴스에 등록된 runners를 등록하거나 사용합니다.
* 로컬 머신에 runner를 생성합니다.

**시작하기:**

* 로컬 머신에 runner 생성하기
* runners에 대해 더 알아보기
