# \[nGrinder] multi endpoint load test 하기

{% hint style="info" %}
3차 테스트는 실제 프로젝트 별 시나리오 바탕으로 진행
{% endhint %}

* api 요청에 필요한 헤더 및 토큰 설정이 필요할 수 있다

{% embed url="https://jane096.github.io/project/ngrinder-performance-test/" %}

{% embed url="https://velog.io/@max9106/nGrinderPinpoint-test2" %}

{% embed url="https://kirinman.tistory.com/102" %}

## 1. multi endpoint 부하테스트 스크립트 실행 순서도

Groovy 스크립트를 이용한 테스트 실행 과정은 다음과 같다:

1. **User** - 사용자는 테스트를 실행하라는 요청을 Controller에 보낸다
2. **Initiates** **Agent** - Controller가 실제 부하를 생성하고, 타겟 서버에 요청을 보내도록 agent에 요청을 보낸다
3. **@BeforeProcess** - 프로세스가 시작하기 전에 한 번 실행되며, 여기서 필요한 설정(예: HTTP 요청의 타임아웃 설정)과 초기화(예: HTTPRequest 객체 생성)가 이루어진다
4. **@BeforeThread** - 각 스레드가 시작하기 전에 실행되며, 각 테스트 메서드(**`test1`**, **`test2`**, **`test3`**)를 GTest 객체에 등록하여 성능 메트릭을 추적하도록 설정한다
5. **@Test** - 실제 테스트가 수행되는 메서드들로, 각각의 HTTP 요청에 대한 응답을 받고 그 결과를 검증한다
   * 예를 들어, `test1,2,3`에서는 데이터 시뮬레이션을 위한 HTTP 요청을 보내고, 응답 코드가 200(OK)인지 확인한다

<figure><img src="../../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>



## 2. multi endpoint 부하테스트용 스크립트 코드

* assertThat
  * `assertThat`은 JUnit 테스트에서 사용되는 검증 메서드 중 하나로, 특정 조건이 참인지 확인하는 역할
    * 여기서 `is(`STATUS\_OK`)`은 Hamcrest 라이브러리의 매처(Matcher)를 사용하여 `statusCode`가 `is` 함수 인자에 있는 HTTP 응답 코드를 확인하여 API 요청이 성공적으로 완료되었는지를 판단
    * 조건이 만족되지 않을 경우 `AssertionError`가 발생하고, 이 에러는 실패한 테스트의 원인을 설명하는 메시지 포함

```groovy
import static net.grinder.script.Grinder.grinder
// Grinder 객체를 정적으로 임포트합니다.

import static org.junit.Assert.*
// JUnit의 Assert 클래스를 정적으로 임포트합니다.

import static org.hamcrest.Matchers.*
// Hamcrest의 Matchers 클래스를 정적으로 임포트합니다.

import net.grinder.script.GTest
// Grinder의 GTest 클래스를 임포트합니다.

import net.grinder.script.Grinder
// Grinder 클래스를 임포트합니다.

import net.grinder.scriptengine.groovy.junit.GrinderRunner
// Grinder의 Groovy JUnit Runner를 임포트합니다.

import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
// BeforeProcess 애노테이션을 임포트합니다.

import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
// BeforeThread 애노테이션을 임포트합니다.

import org.junit.Before
// JUnit의 Before 애노테이션을 임포트합니다.

import org.junit.BeforeClass
// JUnit의 BeforeClass 애노테이션을 임포트합니다.

import org.junit.Test
// JUnit의 Test 애노테이션을 임포트합니다.

import org.junit.runner.RunWith
// JUnit의 RunWith 애노테이션을 임포트합니다.

import org.ngrinder.http.HTTPRequest
// nGrinder의 HTTPRequest 클래스를 임포트합니다.

import org.ngrinder.http.HTTPRequestControl
// nGrinder의 HTTPRequestControl 클래스를 임포트합니다.

import org.ngrinder.http.HTTPResponse
// nGrinder의 HTTPResponse 클래스를 임포트합니다.

import org.ngrinder.http.cookie.Cookie
// nGrinder의 Cookie 클래스를 임포트합니다.

import org.ngrinder.http.cookie.CookieManager
// nGrinder의 CookieManager 클래스를 임포트합니다.

@RunWith(GrinderRunner)
class TestRunner {
    // 필요한 클래스와 객체를 정적으로 선언합니다.
    public static GTest test1, test2, test3
    public static HTTPRequest request
    public static Map<String, String> headers = [:]
    public static List<Cookie> cookies = []
	
    // 상태 코드를 상수로 정의
    private static final int STATUS_OK = 200;
    private static final int STATUS_CREATED = 201;

    // Base URL은 단일 변경점을 갖도록 정적 변수로 선언합니다.
    public static final String BASE_URL = ""

    @BeforeProcess
    public static void beforeProcess() {
        // HTTP 요청의 연결 타임아웃을 5분으로 설정합니다.
        HTTPRequestControl.setConnectionTimeout(300000)

        // 각 엔드포인트에 대한 테스트 객체를 초기화합니다.
        test1 = new GTest(1, "GET /user-load-test/simulate-data")
        test2 = new GTest(2, "GET /user-load-test/simulate-computation")
        test3 = new GTest(3, "GET /user-load-test/simulate-db")

        // 모든 테스트에서 사용할 HTTPRequest 객체를 생성합니다.
        request = new HTTPRequest()

        grinder.logger.info("Setup completed before any processes.")
    }

    @BeforeThread
    public void beforeThread() {
        // 각 테스트 메서드를 해당 GTest 객체에 등록하여 성능 메트릭을 추적할 수 있게 합니다.
        test1.record(this, "test1")
        test2.record(this, "test2")
        test3.record(this, "test3")

        // 스레드가 완료될 때까지 통계 보고를 지연시켜 데이터 손상을 방지합니다.
        grinder.statistics.delayReports = true

        grinder.logger.info("Setup completed before each thread starts.")
    }

    @Test
    public void test1() {
        HTTPResponse response = request.GET(BASE_URL + "/user-load-test/simulate-data");
        assertThat("Test Simulate Data", response.statusCode, is(STATUS_OK));
    }

    @Test
    public void test2() {
        HTTPResponse response = request.GET(BASE_URL + "/user-load-test/simulate-computation");
        assertThat("Test Simulate Computation", response.statusCode, is(STATUS_OK));
    }

    @Test
    public void test3() {
        HTTPResponse response = request.GET(BASE_URL + "/user-load-test/simulate-db");
        assertThat("Test Simulate Database", response.statusCode, is(STATUS_OK));
    }

}
```



## 2. 스크립트를 실행할 테스트 구동

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## 3. 테스트가 구동된 후 파드 scale out

{% hint style="info" %}
targetCPUUtilizationPercentage 549% 로 상승하여 파드 scale out
{% endhint %}

<figure><img src="../../../../.gitbook/assets/image.png" alt=""><figcaption><p>targetCPUUtilizationPercentage 549%</p></figcaption></figure>

## 4. 테스트 종료 후 stabilizationWindowSeconds default 값인 300초 ( 5분 ) 후 scale down 진행

{% hint style="info" %}
targetCPUUtilizationPercentage 1% 로 하락하여 파드 scale down
{% endhint %}

<figure><img src="../../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## 5. nGrinder가 생성한 로그 확인

{% hint style="info" %}
test1,2,3 에 대한 결과 확인
{% endhint %}

```
2024-05-13 15:25:50,224 INFO  
             Tests        Errors       Mean Test    Test Time    TPS          Mean         Response     Response     Mean time to Mean time to Mean time to 
                                       Time (ms)    Standard                  response     bytes per    errors       resolve host establish    first byte   
                                                    Deviation                 length       second                                 connection                
                                                    (ms)                                                                                                    

Test 1       28           0            131.46       137.42       0.39         66.04        25.42        0            0.00         23.96        104.04        "GET /user-load-test/simulate-data"
Test 2       28           0            22.07        4.90         0.39         68.00        26.18        0            0.00         0.21         2.43          "GET /user-load-test/simulate-computation"
Test 3       28           0            5028.29      20.97        0.39         69.00        26.57        0            0.00         0.14         0.14          "GET /user-load-test/simulate-db"

Totals       84           0            1727.27      2335.98      1.16         67.68        78.17        0            0.00         8.11         35.54        
```

