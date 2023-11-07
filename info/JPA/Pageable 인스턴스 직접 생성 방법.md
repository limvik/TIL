# Spring Data JPA 사용 시 Pageable 인스턴스 직접 생성 방법

Spring Data JPA를 이용한다면 `Pageable` 인스턴스를 직접 생성하는 것은 간단합니다. `PageRequest` 클래스를 사용하여 페이지 번호, 페이지 크기, 정렬 방식 등을 지정하여 `Pageable` 객체를 만들 수 있습니다.

아래는 `Pageable` 인스턴스를 생성하는 방법의 예시입니다:

```java
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

// 페이지 번호는 0부터 시작합니다. 
int page = 0; // 첫 번째 페이지
int size = 10; // 페이지 당 데이터 수
Pageable pageable = PageRequest.of(page, size);

// 정렬 조건을 추가하려면 Sort 객체를 사용합니다.
Sort sort = Sort.by(Sort.Direction.ASC, "somePropertyName");
pageable = PageRequest.of(page, size, sort);

// 여러 정렬 기준을 적용하고 싶다면
Sort sortMultiple = Sort.by(Sort.Order.asc("property1"), Sort.Order.desc("property2"));
pageable = PageRequest.of(page, size, sortMultiple);

// 또는 Sort.Direction을 사용하여 정렬 방향을 간단히 명시할 수도 있습니다.
pageable = PageRequest.of(page, size, Sort.Direction.ASC, "propertyName");

// 최신 버전의 Spring Data JPA에서는 Sort.by를 이용해 간편하게 Sort 객체를 생성할 수 있습니다.
pageable = PageRequest.of(page, size, Sort.by("propertyName").ascending());
```

여기서 "propertyName"은 정렬하고자 하는 엔티티의 속성 이름입니다. `Pageable` 인스턴스는 일반적으로 리포지토리 메서드에 파라미터로 전달되어 페이징 처리가 필요한 쿼리에 사용됩니다.

`PageRequest.of` 메서드는 `Pageable`을 구현하는 불변 객체를 반환합니다. 이 객체는 `findAll`, `findBy`, 등의 Spring Data JPA 메서드에 전달될 수 있습니다.

주의할 점은 실제 페이지 번호가 아닌 '0'을 기준으로 한 페이지 인덱스를 사용한다는 것입니다. 예를 들어, 사용자에게 '1'페이지를 보여주려면, '0'을 페이지 파라미터로 제공해야 합니다.

---

출처: Chat GPT