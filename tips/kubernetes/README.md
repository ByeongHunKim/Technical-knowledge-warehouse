# Kubernetes

* `alias 설정`으로 `kubectl` 명령어를 빠르게 칠 수 있다
* `kn` 설정으로 연결된 클러스터의 네임스페이스를 골라서 설정해두어 매번 `kubectl` 명령어에 `-n { namespace }` 를 추가하지 않아도 된다
* `watch` 를 설치하여 1초마다 kn으로 지정한 네임스페이스의 리소스를 모니터링 할 수 있어 배포 테스트할 때 매번 `k get all` 해야하는 번거로움이 사라진다



* `아래 첫번째 쉘 watch -n 1 kubectl get all`
* `두번째 쉘 위에 모니터링을 보면서 새로운 파드가 뜨거나 하면 kubectl 명령어로 리소스의 로그를 보거나 파드에 접속하는 등 액션을 취하면서 업무를 진행한다`
* ![](<../../.gitbook/assets/image (7) (1).png>)



