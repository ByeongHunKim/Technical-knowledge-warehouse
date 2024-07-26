# aws-vpc

* subnet을 구성할 때도 유형이 있는데 private, public subnet으로 나뉜다
  * private
    * 라우팅 테이블을 보면 IGW가 없다
      * DB처럼 통신할 필요가 없으면 NAT GW가 없어도 된다
      *   만약 인터넷 밖으로 나가야 하는 경우는 다이렉트로 나가는 게 아니라 NAT GW를 통해서 NAT GW가 갖고 있는 public ip를 가지고 외부로 통신하게 된다 ( 출발지의 IP를 바꾼다 )&#x20;

          <figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>
  * public
    * 인터넷에서 접근 가능한 서브넷
      * 해당 라우팅 테이블을 보면 vpc 아이피로 목적지가 설정된 경우 밖으로 나가지 않음
      *   반면에 밖에 있는 endpoint 인 경우는 다른 IP 대역이기 때문에  인터넷 게이트웨이로 통해서 외부로 나간다



          <figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>
