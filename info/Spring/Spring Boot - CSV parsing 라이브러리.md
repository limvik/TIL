# Spring Boot에서 사용할 CSV parsing 라이브러리 살펴보기

## 상황

총 295개의 데이터가 있는 거의 변할 일 없는 csv 파일을 업로드하여 parsing 후 데이터베이스에 저장해야 하는 상황입니다. 따라서, csv 파일은 몇 년에 한 번 업로드 할까말까 합니다.

![csv 데이터 예시](./img/CSV%20parsing%20libraries/locations_csv.png)

## 라이브러리 종류

Chat GPT가 추천한 라이브러리는 OpenCSV, Apache Commons CSV, Super CSV, jackson-dataformat-csv 가 있습니다.

Maven 순위권에 있는 것 위주로 추천한 것으로 보입니다.

![Maven 저장소 CSV 라이브러리 순위](./img/CSV%20parsing%20libraries/maven_csv_libraries.png)

출처: [MVN Repository - CSV Libraries](https://mvnrepository.com/open-source/csv-libraries)

Super CSV는 저장소([링크](https://github.com/super-csv/super-csv))를 보니, 마지막 커밋이 2020년 1월이라 유지보수가 안되는 것 같아 제외하고 나머지를 간단하게 살펴보겠습니다.

### OpenCSV

Maven 저장소에서 구 버전과 신 버전을 다 합치면 가장 많은 Usages(724+626=1350)가 됩니다.

#### OpenCSV 사용 예

##### 도메인 객체 생성

@CsvBindByName annotation을 추가해줘야 합니다.

```java
import com.opencsv.bean.CsvBindByName;

public class DomainData {

    @CsvBindByName
    private String column1;

    @CsvBindByName
    private String column2;

    // getters and setters...
}
```

##### Controller 엔드포인트 생성

```java
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;
import com.opencsv.bean.CsvToBeanBuilder;
import java.io.InputStreamReader;
import java.util.List;

@RestController
@RequestMapping("/upload")
public class FileUploadController {

    @PostMapping("/csv")
    public List<DomainData> uploadCsvFile(@RequestParam("file") MultipartFile file) {
        if (file.isEmpty()) {
            throw new RuntimeException("The file is empty.");
        }

        try (InputStreamReader reader = new InputStreamReader(file.getInputStream())) {
            List<DomainData> domainDataList = new CsvToBeanBuilder<DomainData>(reader)
                    .withType(DomainData.class)
                    .build()
                    .parse();

            return domainDataList;
        } catch (Exception e) {
            throw new RuntimeException("Fail to parse CSV file: " + e.getMessage());
        }
    }
}
```

간단한 수준에서는 사용법이 크게 어려워보이지는 않습니다.

#### 기타

OpenCSV는 Dependencies가 상대적으로 많은 편입니다.

![OpenCSV 의존성](./img/CSV%20parsing%20libraries/opencsv_dependencies.png)

출처: [MVN Repository OpenCSV](https://mvnrepository.com/artifact/com.opencsv/opencsv/5.8)

### Apache Commons CSV

[GitHub 링크](https://github.com/apache/commons-csv)를 보면 유지보수가 계속되고 있는 것으로 보입니다.

#### Apache Commons CSV 사용 예

##### Controller 엔드포인트 생성

```java
import org.apache.commons.csv.CSVFormat;
import org.apache.commons.csv.CSVRecord;

@PostMapping("/csv")
public List<DomainData> uploadCsvFile(@RequestParam("file") MultipartFile file) throws IOException {
    Reader in = new InputStreamReader(file.getInputStream());
    Iterable<CSVRecord> records = CSVFormat.DEFAULT
            .withFirstRecordAsHeader()
            .parse(in);

    List<DomainData> dataList = new ArrayList<>();
    for (CSVRecord record : records) {
        DomainData data = new DomainData();
        data.setColumn1(record.get("column1"));
        data.setColumn2(record.get("column2"));
        dataList.add(data);
    }
    return dataList;
}
```

역시나 간단하게 사용할 때는 큰 어려움이 없습니다.

#### 기타

Compile Dependency가 전혀 없습니다. 가볍게 사용하기 좋겠습니다.

![Apache Commons CSV 의존성](./img/CSV%20parsing%20libraries/commons_csv_dependencies.png)

출처: [MVN Repository - Apache Commons CSV](https://mvnrepository.com/artifact/org.apache.commons/commons-csv/1.10.0)

### jackson-dataformats-text

[GitHub 링크](https://github.com/FasterXML/jackson-dataformats-text)를 보면 jackson 가족이라 그런지 유지보수가 계속되고 있습니다.

#### jackson-dataformats-text 사용 예

##### Controller 엔드포인트 생성

```java
import com.fasterxml.jackson.dataformat.csv.CsvMapper;
import com.fasterxml.jackson.dataformat.csv.CsvSchema;

@PostMapping("/csv")
public List<DomainData> uploadCsvFile(@RequestParam("file") MultipartFile file) throws IOException {
    CsvMapper mapper = new CsvMapper();
    CsvSchema schema = CsvSchema.emptySchema().withHeader();
    ObjectReader objectReader = mapper.readerFor(DomainData.class).with(schema);
    MappingIterator<DomainData> it = objectReader.readValues(file.getInputStream());

    return it.readAll();
}
```

가장 간단하게 사용 가능합니다.

#### 기타

jackson 사용시 필요한 라이브러리에 의존성이 있습니다.

![jackson-dataformats-text 의존성](./img/CSV%20parsing%20libraries/jackson_dependencies.png)

## 선택

`jackson-dataformats-text`

### 선택한 이유

Spring Boot는 기본적으로 jackson 라이브러리를 사용하므로, 나중에 필요한 `spring-boot-starter-json` 라이브러리를 추가하면 같이 사용하게될 dependencies 이므로 별 문제가 안된다고 생각됩니다. 익숙한 `@JsonProperty` annotation 쓸 수 있는건 덤 입니다.

```java
@JsonProperty("do-si")
private String dosi;
```

몇 년에 한 번 업로드 하는데 사용하므로, 단독으로 `jackson-dataformats-text` 만 사용해야 한다면 선택하지 않았을 겁니다.

간단하게 사용한다면, `Apache Commons CSV` 가 괜찮아 보입니다.

`OpenCSV`는 dependencies가 상대적으로 많고, `@CsvBindByName` 도 하나하나 붙여줘야해서 OpenCSV 있는 기능이 절대적으로 필요한 경우가 아니라면, 사용하지 않을 것 같습니다. 제가 자세한 사용법은 살펴보지 않아서 다른 방법이 있을 가능성은 있습니다.
