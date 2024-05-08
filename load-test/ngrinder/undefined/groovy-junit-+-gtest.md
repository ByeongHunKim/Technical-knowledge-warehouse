# groovy는 JUnit 스타일에 포함되지 않는건가 ? + GTest가 뭔지 좀 헷갈린다

## groovy는 JUnit 스타일에 포함되지 않는건가 ? + GTest가 뭔지 좀 헷갈린다

Groovy는 JUnit 스타일에 포함될 수 있습니다. Groovy 언어 자체가 Java의 문법을 확장한 것이기 때문에, JUnit과 호환됩니다. 특히 nGrinder는 Groovy 기반의 부하 테스트 스크립트를 작성할 수 있도록 `GTest`라는 클래스와 함께 `GrinderRunner`라는 JUnit 러너를 제공합니다.



#### GTest와 JUnit 스타일의 관계

1. **JUnit 스타일**:
   * JUnit은 Java 기반의 테스트 프레임워크로, `@Test`, `@Before`, `@After` 등의 어노테이션을 사용해 테스트를 작성합니다.
2. **GTest**:
   * nGrinder에서 제공하는 `GTest`는 JUnit 스타일 테스트를 nGrinder 환경에서 실행할 수 있도록 돕는 클래스입니다.
   * `GTest`는 테스트 케이스를 식별하고 통계를 수집하며, 각 테스트를 로깅하는 역할을 합니다.

#### `GTest`의 주요 기능

* **식별**: 각 테스트 케이스를 고유하게 식별하기 위한 `id`와 `name`을 가집니다.
* **기록**: 특정 테스트 메서드를 `record()` 메서드로 기록하여 통계를 수집합니다.

#### 예제 코드

```groovy
import static net.grinder.script.Grinder.grinder
import static org.junit.Assert.*
import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import org.junit.Before
import org.junit.Test
import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPResponse

@RunWith(GrinderRunner)
class TestRunner {

    // 여러 엔드포인트에 대한 GTest 객체 선언
    public static GTest test1
    public static GTest test2
    public static GTest test3
    public static HTTPRequest request

    // 테스트 프로세스 시작 전에 한 번만 실행되는 설정 메서드
    @BeforeProcess
    public static void beforeProcess() {
        test1 = new GTest(1, "GET /test")
        test2 = new GTest(2, "GET /health")
        test3 = new GTest(3, "GET /profile")
        request = new HTTPRequest()
        grinder.logger.info("Before process.")
    }

    // 각 스레드가 시작되기 전에 실행되는 설정 메서드
    @BeforeThread
    public void beforeThread() {
        test1.record(this, "test1")
        test2.record(this, "test2")
        test3.record(this, "test3")
        grinder.statistics.delayReports = true
        grinder.logger.info("Before thread.")
    }

    // 각 테스트 메서드
    @Test
    public void test1() {
        HTTPResponse response = request.GET("https://example.com/test")
        validateResponse(response, 1)
    }

    @Test
    public void test2() {
        HTTPResponse response = request.GET("https://example.com/health")
        validateResponse(response, 2)
    }

    @Test
    public void test3() {
        HTTPResponse response = request.GET("https://example.com/profile")
        validateResponse(response, 3)
    }

    // 공통 응답 검증 메서드
    private void validateResponse(HTTPResponse response, int testNumber) {
        if (response.statusCode == 301 || response.statusCode == 302) {
            grinder.logger.warn("Warning. The response may not be correct. The response code for test {} was {}.", testNumber, response.statusCode)
        } else {
            assertEquals(200, response.statusCode)
            grinder.logger.info("Test {} passed with status code {}", testNumber, response.statusCode)
        }
    }
}
```

#### 주요 구성 요소 설명

* **`@RunWith(GrinderRunner)`**:
  * nGrinder용 JUnit 러너로, Groovy 기반 테스트 실행을 지원합니다.
* **`GTest`**:
  * nGrinder에서 테스트 케이스를 식별하고 기록하는 클래스입니다.
  * `record()` 메서드로 테스트 케이스를 기록하고 통계를 수집합니다.
* **`HTTPRequest`와 `HTTPResponse`**:
  * HTTP 요청을 보내고 응답을 받기 위한 nGrinder의 HTTP 클래스입니다.
* **JUnit 스타일 메서드 어노테이션**:
  * `@BeforeProcess`: 테스트 프로세스가 시작되기 전에 한 번만 실행되는 설정 메서드.
  * `@BeforeThread`: 각 스레드가 시작되기 전에 실행되는 설정 메서드.
  * `@Test`: 실제 테스트를 수행하는 메서드.

**Q1:** JUnit 스타일의 테스트 케이스를 추가로 작성하려면 어떻게 해야 하나요?

**Q2:** GTest와 JUnit 어노테이션을 동시에 사용하는 예제를 더 보여줄 수 있나요?

**Q3:** GTest 객체를 이용해 다른 테스트를 기록하려면 어떤 메서드를 사용해야 하나요?
