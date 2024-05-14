# \[nGrinder] multi endpoint load test script에 정의한 test들이 실행 순서를 보장 받는가?



```groovy
import static net.grinder.script.Grinder.grinder;
import static org.junit.Assert.*;
import static org.hamcrest.Matchers.*;
import net.grinder.script.GTest;
import net.grinder.script.Grinder;
import net.grinder.scriptengine.groovy.junit.GrinderRunner;
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess;
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread;
import org.junit.Before;
import org.junit.BeforeClass;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.ngrinder.http.HTTPRequest;
import org.ngrinder.http.HTTPRequestControl;
import org.ngrinder.http.HTTPResponse;
import org.ngrinder.http.cookie.Cookie;
import org.ngrinder.http.cookie.CookieManager;

import java.util.Map;
import java.util.HashMap;
import java.util.List;

@RunWith(GrinderRunner)
class TestRunner {
    public static GTest test1, test2, test3;
    public static HTTPRequest request;
    public static Map<String, String> headers = new HashMap<>();
    public static List<Cookie> cookies = [];

    private static final int STATUS_OK = 200;
    private static final int STATUS_CREATED = 201;
    
    public static final String BASE_URL = "";
    
    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(300000);

        test1 = new GTest(1, "GET /");
        test2 = new GTest(2, "GET /");
        test3 = new GTest(3, "GET /");

        request = new HTTPRequest();

        grinder.logger.info("Setup completed before any processes.");
    }
    
    @BeforeThread
    public void beforeThread() {
        test1.record(this, "executeTest");
        test2.record(this, "executeTest");
        test3.record(this, "executeTest");

        grinder.statistics.delayReports = true;

        grinder.logger.info("Setup completed before each thread starts.");
    }
    
    @Test
    public void executeTest() {
        String token = testRequest("/", test1);
        testRequestWithToken("/", test2, token);
        testRequest("/", test3);
    }
    
    private String testRequest(String path, GTest test) {
        String testName = "Test " + test.getNumber();
        grinder.logger.info("Starting " + testName);
        HTTPResponse response = request.GET(BASE_URL + path);
        assertThat(testName, response.statusCode, is(STATUS_OK));
        grinder.logger.info("Completed " + testName);
        return response.getText();  // 이 부분을 조정하여 필요한 데이터를 반환하도록 수정
    }

    private void testRequestWithToken(String path, GTest test, String token) {
        String testName = "Test " + test.getNumber();
        grinder.logger.info("Starting " + testName);
        headers.put("Authorization", "Bearer " + token);  // 토큰 사용 예시
        HTTPResponse response = request.GET(BASE_URL + path, headers);
        assertThat(testName, response.statusCode, is(STATUS_OK));
        grinder.logger.info("Completed " + testName);
    }
}

```
