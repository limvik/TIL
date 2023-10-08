# JUnit 을 이용한 테스트 시 추가 정보 출력을 위한 Gradle 설정 방법

Gradle에 아래 설정을 추가합니다.

```groovy
test {
	testLogging {
		events "passed", "skipped", "failed" //, "standardOut", "standardError"

		showExceptions true
		exceptionFormat "full"
		showCauses true
		showStackTraces true

		// Change to `true` for more verbose test output
		showStandardStreams = false
	}
}
```

출처: [Spring Academy](https://spring.academy/courses/building-a-rest-api-with-spring-boot)