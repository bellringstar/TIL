# Spring에서의 Jackson
Spring에서 RESTful 웹 서비스를 개발할 때, JSON 데이터 처리는 필수적입니다. Spring은 기본적으로 Jackson 라이브러리를 사용하여 이 작업을 수행합니다.

## Jackson 동작 원리
### Spring MVC에서의 Jackson 초기화
SpringMVC는 애플리케이션 시작 시 MappingJackson2HttpMessageConverter를 자동으로 등록합니다. 이 컨버터는 HTTP 요청/응답 본문과 JAVA 객체 간의 변환을 담당합니다.
```java
public class MappingJackson2HttpMessageConverter extends AbstractJackson2HttpMessageConverter {
    public MappingJackson2HttpMessageConverter() {
        this(Jackson2ObjectMapperBuilder.json().build());
    }
    // ...
}
```
### ObjectMapper 설정
MappingJackson2HttpMessageConverter는 내부적으로 ObjectMapper를 사용합니다. SpringBoot는 Jackson2ObjectMapperBuilder를 통해 ObjectMapper를 구성합니다.
```java
// 구현 예시 기본값의 경우 날짜/시간을 타임스탬프로 직렬화한다. 이를 방지하기 위해 JavaTimeModule을 등록
@Configuration
public class JacksonConfig {
    @Bean
    public ObjectMapper objectMapper() {
        return new Jackson2ObjectMapperBuilder()
            .featuresToDisable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)
            .modules(new JavaTimeModule())
            .build();
    }
}
```
### 직렬화 과정
1. @ResponseBody 어노테이션이 붙은 메서드의 반환 값을 Jackson이 처리합니다.
2. ObjectMapper가 리플렉션을 사용하여 객체의 구조를 분석합니다.
3. 각 필드에 대해 getter 메서드나 필드 직접 접근을 통해 값을 추출합니다.
4. 추출된 값을 JSON 형식으로 변환합니다.

### 역직렬화 과정
1. @RequestBody 어노테이션이 붙은 파라미터를 Jackson이 처리합니다.
2. JSON 데이터를 파싱하여 내부 트리 구조로 변환합니다.
3. 대상 클래스의 인스턴스를 생성합니다.(기본 생성자 사용)
4. JSON 필드를 객체의 프로퍼티에 매핑합니다.

### 주의사항
1. 필드 접근성 관련 주의사항
- Getter 메서드 부재
  - getter가 없으면 필드가 직렬화되지 않을 수 있음
  - 해결책
    - getter 메서드 추가
    - @JsonProperty 어노테이션 사용
    - @JsonAutoDetect 어노테이션으로 필드 직접 접근 허용
- Private 필드
  - Jackson의 기본 설정은 private 필드에 직접 접근하지 않습니다.
  - 해결책
    - @JsonAutoDetect
    - ObjectMapper 설정 변경
- Transient 필드
  - transient 키워드가 붙은 필드는 직렬화되지 않음
  - 해결책
    - 필요 시 @JsonProperty로 강제 직렬화
2. 생성자 및 객체 생성 관련 주의사항
- 기본 생성자 부재
  - 기본 생성자가 없으면 역직렬화 실패
  - 해결책
    - 기본 생성자 추가
    - @JsonCreator 사용 
- 불변 객체 처리
  - final 필드, setter 없는 객체 역직렬화 어려움
  - 해결책
    - @JsonCreator와 @JsonProperty 조합 사용
    - Builder 패턴 적용
3. 이름 불일치 관련 주의사항
- JSON 프로퍼티명과 Java 필드명 불일치
- 해결책
  - @JsonProperty
  - PropertyNamingStrategy 설정
4. 타입 불일치 관련 주의사항
- JSON 데이터 타입과 Java 필드 타입 불일치
- 해결책
  - Custom Deserializer
  - @JsonDesirialize
5. 날짜/시간 타입 처리
- java.util.Date나 java.time 패키지의 클래스들이 기본적으로 타임스탬프로 직렬화
- 해결책
  - JavaTimeModule 등록
  - @JsonFormat 어노테이션 사용