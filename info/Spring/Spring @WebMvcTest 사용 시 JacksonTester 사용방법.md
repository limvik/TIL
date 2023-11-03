# Spring @WebMvcTest 사용 시 JacksonTester 사용방법

## 문제

@WebMvcTest를 사용하면 `@JsonTest`를 추가할 수 없어 annotation 만으로 JacksonTester를 사용할 수 없습니다.

## 해결 방법

다음과 같이 ObjectMapper를 Auto Wiring 하고, JacksonTester 를 직접 설정해줍니다.

```java
@WebMvcTest(controllers = ReviewController.class)
public class ReviewControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    JacksonTester<ReviewRequest> requestJson;

    @BeforeEach
    public void setup() {
        JacksonTester.initFields(this, objectMapper);
    }

    @Test
    void test() {
        int testScore = 5;
        String testContent = "맛있네요.";
        var testReviewRequest = new ReviewRequest(testScore, testContent);
        String tesetReviewRequestJson = requestJson.write(testReviewRequest).getJson();
        // ... some test
    }

}
```

테스트용 JSON 직접 만들기 귀찮을 때 사용하였습니다.

출처: ChatGPT