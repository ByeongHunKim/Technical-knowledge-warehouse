# Groovy Script 실행 구조 분석



<figure><img src="../../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

#### nGrinder 테스트 실행 구조 (Groovy Script Engine)

1. **Groovy Script Engine**
   * Groovy 기반의 스크립트 엔진으로 각 프로세스를 생성하고 관리.
   * 각 프로세스는 여러 개의 스레드를 가질 수 있습니다.
2. **Process (프로세스)**
   * 프로세스는 하나의 독립적인 테스트 실행 단위로, 각 프로세스는 여러 개의 스레드를 생성.
   * 어노테이션: `@BeforeProcess`, `@AfterProcess`
   * **역할**:
     * 테스트 시작 전 전역 설정을 처리하거나 종료 후 정리 작업을 수행.
3. **Thread (스레드)**
   * 프로세스 내에서 병렬로 실행되는 부하 테스트 단위.
   * 각 스레드는 고유한 테스트 객체(Test Case Object)를 통해 테스트를 수행.
   * 어노테이션: `@BeforeThread`, `@AfterThread`
   * **역할**:
     * 각 스레드 시작 전 스레드별 초기화 작업을 수행.
     * 스레드 종료 후 정리 작업을 수행.
4. **Test Case Object (테스트 케이스 객체)**
   * 각 스레드에서 실행되는 테스트 케이스 객체입니다.
   * 각 테스트 메서드(`@Test` 어노테이션)를 반복하여 실행하며 부하 테스트를 수행합니다.
   * 어노테이션: `@Before`, `@After`, `@Test`

#### 전체 실행 플로우

1. **Groovy Script Engine**: 스크립트 엔진이 프로세스를 생성하고 실행을 시작.
2. **Process (프로세스)**: `@BeforeProcess` 어노테이션 메서드가 먼저 실행.
3. **Thread (스레드)**: 프로세스 내에서 각 스레드를 생성하고, `@BeforeThread` 실행.
4. **Test Case Object**:
   * 각 스레드별로 테스트 케이스 객체를 생성.
   * `@Before`, `@Test`, `@After` 메서드를 순서대로 실행하며 테스트를 수행.
   * 테스트 메서드(`@Test`)는 반복하여 실행되며 부하 테스트를 수행.
5. **정리 작업**:
   * 모든 스레드 실행 후 `@AfterThread` 메서드가 실행.
   * 모든 프로세스가 종료된 후 `@AfterProcess` 메서드가 실행.

#### 이미지 설명 및 스크립트 적용 예시

**이미지 플로우 요약:**

1. Groovy Script Engine이 프로세스를 생성.
2. 각 프로세스에서 다수의 스레드를 생성.
3. 각 스레드는 `@BeforeThread`, `@Test`, `@AfterThread`를 통해 테스트 케이스 객체를 실행.
4. 각 테스트 케이스 객체에서 반복적인 부하 테스트 수행.

**스크립트 예시:**

```groovy
import static net.grinder.script.Grinder.grinder
import static org.junit.Assert.*
import static org.hamcrest.Matchers.*
import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import org.junit.Before
import org.junit.Test
import org.junit.runner.RunWith

import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPResponse
import org.ngrinder.http.cookie.CookieManager

@RunWith(GrinderRunner)
class TestRunner {

    public static GTest test
    public static HTTPRequest request
    public static Map<String, String> headers = [:]
    public static Map<String, Object> params = [:]

    @BeforeProcess
    public static void beforeProcess() {
        test = new GTest(1, "airdrop-api.dev.memecore.org")
        request = new HTTPRequest()
        grinder.logger.info("Before Process.")
    }

    @BeforeThread
    public void beforeThread() {
        test.record(this, "test")
        grinder.statistics.delayReports = true
        grinder.logger.info("Before Thread.")
    }

    @Before
    public void before() {
        request.setHeaders(headers)
        CookieManager.addCookies([])
        grinder.logger.info("Before Test. Initialize headers and cookies.")
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET("https://airdrop-api.dev.memecore.org", params)

        if (response.statusCode == 301 || response.statusCode == 302) {
            grinder.logger.warn("Warning. The response may not be correct. The response code was {}.", response.statusCode)
        } else {
            assertThat(response.statusCode, is(200))
        }
    }
}
```

**Q1:** nGrinder에서 테스트 프로세스와 스레드 개수를 조절하려면 어떻게 해야 하나요?

**Q2:** 스레드 및 프로세스 간 데이터를 공유하려면 어떻게 해야 하나요?

**Q3:** `@AfterProcess` 및 `@AfterThread` 메서드의 역할은 무엇인가요?
