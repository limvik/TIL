# Spring @WebMvcTest 에서 Spring Security Configuration 해제방법

## 문제

@WebMvcTest로 Controller에 대한 슬라이스 테스트 수행 시, 개발초기에 아래와 같이 Spring Security 설정에서 모든 요청을 허용해 놓아도 반영되지 않는 문제가 있습니다.

```java
@EnableWebSecurity
@Configuration
public class SecurityConfig {
    @Bean
    public WebSecurityCustomizer webSecurityCustomizer() {
        return (web) -> web.ignoring().anyRequest();
    }
}
```

## 해결 방법

다음과 같이 @WebMvcTest annotatio에 `excludeAutoConfiguration = SecurityAutoConfiguration.class`를 추가해줍니다.

```java
@WebMvcTest(controllers = LocationController.class, excludeAutoConfiguration = SecurityAutoConfiguration.class)
public class LocationControllerTest {

    @Autowired
    MockMvc mockMvc;

    @MockBean
    LocationService locationService;

    // ... some test
}
```

출처: [Disable Spring Security Configuration for @WebMvcTest](https://www.appsdeveloperblog.com/disable-spring-security-configuration-for-webmvctest/)