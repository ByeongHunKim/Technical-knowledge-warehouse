# MR

## CI/CD slack notifiaction 구축작업 MR&#x20;

#### as-is

1. 배포 및 빌드 실패 직접 gitlab pipeline 링크에서 확인
2. 계속 기다림
3. 실패 시 링크를 직접 복사해서 @tech-devops 호출

#### to-be

1. 빌드 및 배포 실패
2. ingration 채널에 실패 메시지 알림
3. 해당 메시지 링크를 복사해서 part-devops-operation 채널에 붙여놓고 @tect-devops 호출 또는 해당 메시지 스레드에 @tect-devops 호출
4. 배포 시작 시 오는 메시지의 링크를 클릭해서 runtime log (loki URL) 확인
5. 성공 시 오는 메시지의 링크를 클릭해서 변경사항 확인

***

### 변경사항

#### 1. start, build-check stages 추가

*   before

    <figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
*   after

    <figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>
* start : pipeline이 시작되었는 알림을 연결된 슬랙 채널에 전송
*   build-check : build stage가 실패했을 때만 동작하여 실패 알림을 연결된 슬랙 채널에 전송

    <figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

#### 2. review stage에 common\_after,before\_script 추가

* common\_before\_script : 해당 pipeline에 맞는 ( 프로젝트, 환경 ) runtime log를 볼 수 있게 loki URL을 설정해서 연결된 슬랙 채널에 전송
* common\_after\_script : review stage의 성공 및 실패 여부에 따라 알맞은 메시지를 연결된 슬랙 채널에 전송

#### 3. 공통

* SLACK\_WEBHOOK\_URL이 설정되지 않은 경우 배포가 안되는 상황을 방지하기 위해 경고 메시지를 출력하고 Slack 알림을 건너뜀
* main(추후 eks-main), development 브랜치는 eks-dev에 배포, 기타 모든 브랜치는 play cluster로 배포될 수 있도록 Build.gitlab-ci.yml 수정

#### 4. 메시지 구성

* integration 채널이 프로젝트 별로 존재하는 것이 아닌 팀별로 존재하기 때문에 어떤 project인지 알기 위해 가장 앞에 project repo name 추가
* 브랜치 별로 어떤 pipeline 환경인지 명시 ( main - prod, development - dev, etc - branch name )
* pipeline을 실행 시킨 gitlab 유저명 추가
* stage(build,deploy) 실패 시 실패한 job을 확인할 수 있는 링크 추가
* stage(deploy) 성공 시 프로젝트 url을 확인할 수 있는 링크 추가

#### 4.1 pipeline 성공 ( feature branch )

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### 4.2 pipeline 성공 ( development branch )

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

#### 4.3 pipeline 성공 ( main branch )

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

#### 4.4 pipeline 실패 ( 빌드 )

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

#### 4.5 pipeline 실패 ( 배포 )

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### 시도 했지만 실패한 작업

* retry 설정&#x20;
  * gitlab auto deploy values 설정 값에 Probe 관련 retry 값이 없음, startProbe 관련한 retry 값 추가해도 변화없음
  * gitlab ci 설정을 하면 설정한 retry값만큼 해당 job이 다시 돌아 필요 없음
* 채널 알림을 꺼두고 멘션이 울렸을 때만 확인하도록 메시지에 유저를 태그&#x20;
  * 단순히 이름이 아니라, slack ID를 매핑해줘야 하는 이슈로 실패
* staging enalbed 하여 development 는 staging으로 배포
  * review stage랑 차이가 없어서 롤백
* development branch 현행화
