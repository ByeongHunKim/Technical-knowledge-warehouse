# Groovy script

nGrinder는 기본적으로 **Groovy** 스크립트를 사용하지 않는다. 그러나 nGrinder에서는 The Grinder의 기존 기능을 확장하여 다양한 스크립트 작성 방식을 제공한다. 특히, **Jython**을 기본 스크립트 언어로 사용한다.

이외에도 Groovy 스타일로 작성하려면 nGrinder의 JUnit 스크립트 기능을 활용할 수 있다. nGrinder 자체적으로 Groovy 스타일을 지원하는 것은 아니지만, JUnit을 통한 테스트 스크립트 작성이 가능하다.

**JUnit 스타일 예시 ( java) :**

JUnit 테스트는 아래와 같이 Groovy 스타일과 유사한 구조로 작성할 수 있다.

```java
// JUnit 스크립트 예시 (Java)

import static net.grinder.script.Grinder.grinder;
import net.grinder.plugin.http.HTTPRequest;
import net.grinder.plugin.http.HTTPResponse;
import org.junit.Before;
import org.junit.Test;
import static org.junit.Assert.assertTrue;

public class TestRunner {
    private HTTPRequest request;

    @Before
    public void setUp() {
        request = new HTTPRequest();
        grinder.logger.info("Setting up the test.");
    }

    @Test
    public void testRequest() {
        HTTPResponse response = request.GET("http://example.com");

        assertTrue("Expected status 200, got " + response.getStatusCode(),
                   response.getStatusCode() == 200);

        if (response.getStatusCode() == 200) {
            grinder.logger.info("Request successful.");
        } else {
            grinder.logger.warn("Request failed.");
        }
    }
}

```



**Groovy 예시:**

Groovy는 Java 문법과 유사하지만, 더 간결하고 스크립트 언어로 작성할 수 있습니다.

```groovy
// Groovy 스크립트 예시

import static net.grinder.script.Grinder.grinder
import net.grinder.plugin.http.HTTPRequest
import net.grinder.plugin.http.HTTPResponse
import org.junit.Before
import org.junit.Test
import static org.junit.Assert.assertTrue

class TestRunner {
    private HTTPRequest request = new HTTPRequest()

    @Before
    void setUp() {
        grinder.logger.info("Setting up the test.")
    }

    @Test
    void testRequest() {
        HTTPResponse response = request.GET("http://example.com")

        assertTrue("Expected status 200, got ${response.getStatusCode()}",
                   response.getStatusCode() == 200)

        if (response.getStatusCode() == 200) {
            grinder.logger.info("Request successful.")
        } else {
            grinder.logger.warn("Request failed.")
        }
    }
}

```



#### **Groovy와 Java 비교**

**결론:** nGrinder는 Groovy 스크립트를 직접 지원하지는 않지만, Java 기반의 JUnit 스크립트가 Groovy 스타일과 유사하게 작성될 수 있습니다.

#### 질문에 대한 답변

**Q1:** nGrinder에서 스크립트를 작성할 때 Groovy 언어를 사용할 수 있나요?

* **답변:** Groovy 스크립트는 nGrinder에서 직접적으로 지원되지 않는다. 하지만, Java 기반 JUnit 스크립트가 Groovy와 유사한 구조로 작성될 수 있습니다.

**Q2:** Groovy 스타일로 부하 테스트 스크립트를 작성하려면 어떻게 해야 하는가?

* **답변:** Groovy 스타일로 작성하려면 Java JUnit 스타일로 스크립트를 작성하거나, Jython을 사용해 GTest 스타일 스크립트를 작성할 수 있습니다.

**Q3:** nGrinder에서 사용되는 스크립트 언어와 각각의 장단점은 무엇인가요?

* **답변:**
  * **JUnit 스타일 (Java):**
    * **장점:** Java 언어의 풍부한 라이브러리와 기능 활용 가능.
    * **단점:** 작성 시 더 복잡할 수 있음.
  * **GTest 스타일 (Jython):**
    * **장점:** Python 스타일로 더 간결한 스크립트 작성 가능.
    * **단점:** Java 라이브러리를 사용할 때 일부 기능 제약이 있을 수 있음.

**Q1:** nGrinder에서 다른 스크립트 언어를 사용해 부하 테스트를 작성할 수 있는 방법은 무엇인가?

**Q2:** Jython 스타일로 복잡한 테스트 시나리오를 작성하려면 어떻게 해야 하나?

**Q3:** Groovy 스타일의 부하 테스트를 실제로 구현한 사례가 있는가?





**GTest 스타일 예시:**

기본적으로 제공되는 `GTest` 방식의 예제 스크립트는 다음과 같다.

```python
# Jython 스크립트 예시 (GTest 스타일)

from net.grinder.script.Grinder import grinder
from net.grinder.plugin.http import HTTPRequest

class TestRunner:
    def __init__(self):
        self.request = HTTPRequest()

    def beforeTest(self):
        grinder.logger.info("Before Test.")

    def test(self):
        response = self.request.GET("http://example.com")

        if response.getStatusCode() == 200:
            grinder.logger.info("Request successful.")
        else:
            grinder.logger.warn("Request failed.")

        assert response.getStatusCode() == 200

```

#### nGrinder 스크립트 종류 정리:

* **JUnit 스타일 (Java):** Java로 작성된 표준 JUnit 기반의 스크립트.
* **GTest 스타일 (Jython):** Python의 Jython 인터프리터를 사용하여 작성하는 스크립트.

#### 요약:

1. **Groovy 지원 여부:**
   * nGrinder 자체적으로 Groovy 스크립트를 직접 지원하지는 않으나, Java 기반 JUnit 스타일 스크립트를 통해 Groovy 스타일과 유사한 형태로 작성 가능.
2. **nGrinder 지원 스크립트 종류:**
   * Jython 기반 GTest 스타일
   * Java 기반 JUnit 스타일

**Q1:** Jython을 이용해 더 복잡한 GTest 스타일 스크립트를 작성하려면 어떻게 해야 하나요?

**Q2:** JUnit 스타일 스크립트에서 테스트 데이터를 어떻게 설정하고 관리할 수 있나요?

**Q3:** nGrinder 스크립트 작성 시 외부 라이브러리를 사용할 수 있는 방법은 무엇인가요?
