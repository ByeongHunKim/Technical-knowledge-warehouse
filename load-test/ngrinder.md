# nGrinder

{% embed url="https://github.com/naver/ngrinder/releases/tag/ngrinder-3.5.9-20230227" %}

{% embed url="https://download.oracle.com/java/22/latest/jdk-22_macos-aarch64_bin.dmg" %}

#### nGrinder 자세한 설명 및 구성 이해하기

**1. nGrinder란 무엇인가?**

nGrinder는 NHN이 개발한 오픈소스 부하 테스트 플랫폼으로, 기존에 존재하는 The Grinder를 확장하여 대규모 부하 테스트 및 분산 부하 테스트가 가능. 웹 인터페이스를 통해 부하 테스트 스크립트를 생성, 관리하며 다수의 에이전트 서버를 통해 분산 부하 테스트를 수행.



**nGrinder의 주요 기능:**

* 다양한 스크립트 언어 지원: Jython, Groovy
* 실시간 모니터링 및 레포트
* 동적 에이전트 할당을 통한 확장성
* 에이전트 병렬 구동 및 분산 테스트



**2. 에이전트를 따로 구동해야 하는 이유 및 동작 원리**

**에이전트 구동 필요 이유:**

* **확장성**: nGrinder Controller는 테스트 관리를 담당하며, 실제로 부하를 발생시키는 것은 에이전트.
* **분산 테스트**: 여러 대의 에이전트를 통해 다수의 서버에 동시에 요청을 보낼 수 있어 대규모 부하 테스트에 적합.

**에이전트 동작 구조:**

1. **Controller에서 명령 전송**: Controller에서 부하 테스트 스크립트와 설정 정보를 에이전트에 전송.
2. **에이전트 실행**: 각 에이전트는 스크립트를 기반으로 부하 테스트를 수행.
3. **테스트 결과 수집**: 테스트 결과를 Controller로 보내고, Controller는 실시간 모니터링 및 레포트 제공.



**3. 스크립트 동작 구조**

스크립트는 각 에이전트에서 테스트할 내용을 정의하며, `GTest` 또는 `JUnit, Groovy` 스타일로 작성 가능.



```groovy
// Groovy 스타일 예시
import static net.grinder.script.Grinder.grinder
import net.grinder.plugin.http.HTTPRequest
import net.grinder.plugin.http.HTTPResponse

public class TestRunner {
    HTTPRequest request = new HTTPRequest()

    @BeforeProcess
    public static void beforeProcess() {
        grinder.logger.info("Before Process.")
    }

    @BeforeThread
    public void beforeThread() {
        grinder.statistics.delayReports = true
        grinder.logger.info("Before Thread.")
    }

    @Before
    public void before() {
        grinder.logger.info("Before Test.")
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET("http://example.com")

        if (response.statusCode == 200) {
            grinder.logger.info("Request successful.")
        } else {
            grinder.logger.warn("Request failed.")
        }

        assert response.statusCode == 200
    }
}

```

#### **테스트 과정 설명**

1. **스크립트 작성**
   * 원하는 부하 테스트 대상에 따라 `GTest`, `JUnit` 또는 `Groovy`로 작성.
2. **스크립트 업로드**
   * nGrinder 웹 인터페이스를 통해 스크립트를 업로드하고 테스트를 구성.
3. **테스트 실행**
   * 에이전트 선택 및 스크립트 설정 후, 부하 테스트 실행.
4. **결과 확인 및 분석**
   * 웹 인터페이스를 통해 실시간 모니터링 및 테스트 완료 후 레포트 분석.

#### 요약:

* **nGrinder**는 대규모 부하 테스트를 위한 오픈소스 플랫폼으로, 확장성과 분산 부하 테스트 기능이 주요 특징.
* **Controller-에이전트** 구조로 테스트를 관리하며, 에이전트가 실제 부하를 발생시킴.
* 스크립트 작성 및 실행을 통해 웹 인터페이스로 실시간 모니터링과 결과 분석 가능.

**Q1:** nGrinder의 각 스크립트 유형별 작성 및 활용 예시를 보여줄 수 있는가?

**Q2:** nGrinder를 사용하여 테스트 시나리오별 테스트 전략을 어떻게 수립할 수 있는가?

**Q3:** nGrinder와 JMeter의 차이점 및 적합한 사용 사례는 무엇인가?
