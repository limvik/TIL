# TestRestTemplate 사용 시 cannot retry due to server authentication, in streaming mode 오류 해결 방법

## 상황

TestRestTemplate 사용 시 아래와 같은 오류가 발생하였습니다.

cannot retry due to server authentication, in streaming mode

## 이유

구체적인 이유는 찾지 못했지만, TestRestTemplate 에서 사용하는 HTTP 요청을 수행하는 클래스가 401 응답을 잘 처리하지 못하는 것으로 보입니다.

특히 body 부분을 접근할 때 문제가 생깁니다. body 없이 응답을 보내면 오류가 없습니다.

## 해결방법

Spring Boot 3 버전의 경우 HttpClient5, Spring Boot 2 버전의 경우 HttpClient4 을 dependency에 추가합니다.

추가만 하고, 다른건 안해줘도 됩니다. Spring Boot 2 버전에서는 확인해보지 못했지만, 다른 분 글을 보니 된다고 합니다.

```groovy
// For Spring Boot 3
implementation 'org.apache.httpcomponents.client5:httpclient5:5.2.1'

// For Spring Boot 2
implementation 'org.apache.httpcomponents:httpclient:4.5.14'
```

```xml
<!-- For Spring Boot 3 -->
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
    <version>5.2.1</version>
</dependency>

<!-- For Spring Boot 2 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.14</version>
</dependency>
```

[HttpClient5 Maven Repository 링크](https://mvnrepository.com/artifact/org.apache.httpcomponents.client5/httpclient5)
[HttpClient4 Maven Repository 링크](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

PATCH 메서드 사용할 때 추가하려고 Dependency에 추가하는걸 미루고 있었는데, 이전에는 매번 미리 추가해서 이런 오류를 보지 못했었나 봅니다.

출처 및 참고자료
1. [Getting java.net.HttpRetryException: cannot retry due to server authentication, in streaming mode](https://stackoverflow.com/questions/49119354/getting-java-net-httpretryexception-cannot-retry-due-to-server-authentication)
2. [RestClientException client shows empty response body for Spring Client authentication (possible bug)](https://github.com/spring-attic/spring-security-oauth/issues/441)