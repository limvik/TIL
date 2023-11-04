# Spring Boot Test - MultipartFile Mocking 방법

## 상황

아래와 같이 파일을 업로드 하기 위해 csv 파일을 MultipartFile로 수신하는 Controller가 있어, 테스트를 수행하려면 MultipartFile Mocking이 필요합니다.

```java
@PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public ResponseEntity<LocationUploadResponse> uploadLocationFile(@RequestParam("file") MultipartFile file)
        throws IOException {

    // ... some code
}
```

## MultipartFile Mocking 방법

아래와 같이 `Resource`, `ClassPathResource` 를 이용하여 `test` 디렉토리의 `resources` 에 있는 파일을 불러옵니다.

그리고 MockMultipartFile 인스턴스를 생성합니다. 첫 번째 인수는 key 입니다. Controller에서 @RequestParam으로 지정한 값과 같습니다.

다음은 filename, mediatype, file stream 또는 byteArray 로 인수를 채워줍니다. stream으로 입력하면 MockMultipartFile 내부에서 다시 또 byteArray로 변환하므로, 그냥 byteArray로 입력합니다.

```java
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;
// ...

void shouldSaveUploadedCsvFileAndReturn201() throws Exception {
    Resource resource = new ClassPathResource("locations.csv");
    MockMultipartFile file = new MockMultipartFile(
            "file",
            resource.getFilename(),
            "text/csv",
            resource.getContentAsByteArray());

    mvc.perform(multipart(HttpMethod.POST, "/api/v1/locations")
                    .file(file)
                    .contentType(MediaType.MULTIPART_FORM_DATA_VALUE))
            .andExpect(status().isCreated())
            .andExpect(header().string("Location", "/api/v1/locations"))
            .andExpect(jsonPath("$.['countTotal']").value(295));
}
```

출처: [spring.io - Uploading Files](https://spring.io/guides/gs/uploading-files/)