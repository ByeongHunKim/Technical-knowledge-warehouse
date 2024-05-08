# Groovy script deep dive

nGrinder 스크립트에는 다양한 어노테이션이 사용됩니다. 특히 `@BeforeProcess`, `@BeforeThread`, `@Before`, `@Test` 등의 어노테이션이 주요하게 사용되며, 각각의 역할을 이해하는 것이 중요합니다.

#### 기본 개념 정리

**프로세스와 스레드**

1. **프로세스**:
   * 운영체제에서 독립적으로 실행되는 프로그램.
   * 각 프로세스는 고유한 메모리 공간을 갖고 실행됩니다.
   * nGrinder에서 하나의 부하 테스트 실행은 하나의 프로세스로 간주됩니다.
2. **스레드**:
   * 프로세스 내에서 실행되는 독립적인 작업 흐름.
   * 하나의 프로세스에는 여러 개의 스레드가 포함될 수 있습니다.
   * nGrinder의 각 스레드는 병렬로 부하를 생성하는 단위입니다.

#### 어노테이션 역할 설명

**1. `@BeforeProcess`**

* **역할**: 프로세스 전체에서 한 번만 실행됩니다.
* **사용 시점**: 모든 스레드가 실행되기 전에 프로세스 단위로 필요한 설정을 할 때 사용.
* **예시 코드**:

```groovy
@BeforeProcess
public static void beforeProcess() {
    HTTPRequestControl.setConnectionTimeout(300000)
    test = new GTest(1, "airdrop-api.dev.memecore.org")
    request = new HTTPRequest()
    grinder.logger.info("Before Process.")
}
```

**2. `@BeforeThread`**

* **역할**: 각 스레드가 시작되기 전에 실행됩니다.
* **사용 시점**: 각 스레드별 초기화 작업을 할 때 사용.
* **예시 코드**:

```groovy
@BeforeThread
public void beforeThread() {
    test.record(this, "test")
    grinder.statistics.delayReports = true
    grinder.logger.info("Before Thread.")
}
```

**3. `@Before`**

* **역할**: 각 테스트 메서드 실행 전에 실행됩니다.
* **사용 시점**: 각 테스트에서 공통적으로 필요한 설정을 할 때 사용.
* **예시 코드**:

```groovy
@Before
public void before() {
    request.setHeaders(headers)
    CookieManager.addCookies(cookies)
    grinder.logger.info("Before Test. Initialize headers and cookies.")
}
```

**4. `@Test`**

* **역할**: 실제 테스트를 수행하는 메서드입니다.
* **사용 시점**: 테스트하고자 하는 부하 테스트 코드를 작성할 때 사용.
* **예시 코드**:

```groovy
@Test
public void test() {
    HTTPResponse response = request.GET("https://airdrop-api.dev.memecore.org", params)

    if (response.statusCode == 301 || response.statusCode == 302) {
        grinder.logger.warn("Warning. The response may not be correct. The response code was {}.", response.statusCode)
    } else {
        assertThat(response.statusCode, is(200))
    }
}
```

#### 부하 테스트 구조 요약

* **프로세스**: 하나의 테스트 실행 단위.
  * `@BeforeProcess`로 초기 설정 수행.
* **스레드**: 프로세스 내에서 병렬로 실행되는 부하 단위.
  * `@BeforeThread`로 스레드별 초기화 작업 수행.
* **테스트**: 각 스레드가 수행하는 개별 테스트 작업.
  * `@Before`로 각 테스트에 필요한 공통 설정 수행.
  * `@Test`로 실제 부하 테스트 코드를 작성.

#### 어노테이션을 이용한 테스트 스크립트 예시

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

#### 추가 질문

**Q1:** 스크립트에서 프로세스와 스레드의 개수를 어떻게 조절하나?

**Q2:** 각 테스트 메서드의 실행 결과를 레포트에 표시하는 방법은 무엇인가?

**Q3:** nGrinder에서 스레드 간 데이터를 공유하거나 프로세스 간 데이터를 공유할 수 있나?
