# CI/CD Notifications

{% embed url="https://medium.com/@mrdevx/step-by-step-guide-integrating-slack-with-gitlab-ci-for-seamless-ci-cd-notifications-be76a54d3038" %}

***

## trouble shooting

* start, finalize stage로 구현하려고 했는데, finalize stage에서 전체 파이프라인의 성공여부를 확인할 수 있는 predefined variable이 없고 $CI\_JOB\_STATUS 값이 있어서 해당 stage의 common after script에서 저 값으로 판단하여 deploy ( review, production ) stage에서 최종 성공 여부에 대한 메시지를 전달
* start에서 이미지를 사용하지 않으면 curl 미설치 에러가 발생
  * ubuntu:latest 를 사용하고 before\_script에서 curl 설치를 했으나 어쩔땐 이 간략한 stage에서 2분이나 걸림
  * curl 만 설치된 base image 참조
* slack webhook url이 없는 상태인 경우 배포 pipeline이 안되는 것을 방지하기 위해서 allow\_failure : true 추가
  *   &#x20;설정을 하면 에러가 나도 스킵되고 나머지 stage가 진행됨

      <figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>
* 메시지 간결화
  * 내가 구상한 integration 채널로 가는 게 아니라 team-prjname-integration 하나로 우선 가자고 하셔서 이에 맞게 메시지를 간결하고 직관적으로 구현

![](<../.gitbook/assets/image (4).png>)



***

## 추가 개선 사항

* 성공했을 때는 프로젝트  url, 실패했을 때는 실패한 Job url을 보여주도록 개선
  * deploy를 실패시키기 위해 livenessprobe를 /로 잡아놨는데 api endpoint를 바꿈
  *

      <figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>
  * ![](<../.gitbook/assets/image (2).png>)
    * Liveness probe failed: HTTP probe failed with statuscode: 404
  * 메시지 failed 확인 및 링크 누를 시 실패한 review stage로 이동 확인
    *   추후에 여기에 스레드에서 데브옵스 호출 하는 식으로

        <figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
    *   성공했을 때는 프로젝트 url

        <figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
    *   실패했을 때는 실패한 job link

        <figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
*   너무 길어서 retry 설정이 필요해보임

    <figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>



    * retry: max: 2 when: - always # 모든 종류의 실패에 대해 재시도 추가\

      *   이걸 하면 파드에서 retry하는 게 아니라 gitlab에서 retry 하는 설정 이었음

          <figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>
* common before script로 loki url 프로젝트 배포 환경에 맞게 전달해서 로그를 바로 볼 수 있게 개선
* staging deploy를 돌려보면서 적용할 수 있는 지 확인
  * 목표는 feature 브랜치에서 development로 merge된 경우 build stage를 생략하고 바로 deploy가 진행
  * 현재는 production ( main ) 브랜치만 production stage고 나머지는 모두 review stage 임
