# TestRestTemplate 사용 시 cannot retry due to server authentication, in streaming mode 오류 해결 방법

## 상황

TestRestTemplate를 사용하여 401(Unauthorized)를 반환하는 요청에 대한 엔드포인트를 테스트하고 있었습니다.

그런데 HttpRetryException이 던져지고, 아래와 같은 오류가 발생하였습니다.

> cannot retry due to server authentication, in streaming mode

## 이유

TestRestTemplate은 HTTP 연결 시 Native Java API인 `HttpURLConnection`을 사용합니다. 기본적으로 Streaming Mode를 사용하여 HTTP 요청에 대한 응답을 버퍼에 저장하지 않고 즉시 처리합니다.

API 문서를 참조해보면, 아래와 같이 authentication 이나 redirection이 필요한 경우, 응답을 읽으려고 할 때 `HttpRetryException`이 던져짐을 언급하고 있습니다.

> A HttpRetryException will be thrown when reading the response if authentication or redirection are required.

401(Unauthorized)를 반환하는 경우 인증이 필요한 상황이고 response body에 접근하는 경우 HttpRetryException이 던져집니다.

- 참고자료
  - https://bugs.openjdk.org/browse/JDK-8118819?page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel&showAll=true
  - https://docs.oracle.com/javase/8/docs/api/java/net/HttpURLConnection.html#setFixedLengthStreamingMode-long-

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
