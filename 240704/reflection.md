# Reflection
리플렉션은 실행 중인 자바 프로그램이 자기 자신을 검사하거나 내부의 속성을 조작할 수 있게 해주는 기능입니다.
이를 통해 프로그램은 컴파일 시간에 알 수 없엇던 클래스, 인터페이스, 필드, 메서드에 접근할 수 있습니다.<br>
-> 한마디로 런타임 시간에 자기 자신에 대해 확인하고 조작할 수 있도록 해주는 건가?<br>
우선 런타임 때 이런걸 해야 하는 이유가 뭘까? 어떤 경우에 사용하게 되는걸까.. 
컴파일 타임과 런타임 때가 다르다 라는 데서 먼저 떠오르는건 다형성인데.. 인터페이스에 의존하고 있다가 런타임 시에는 구체 클래스에 의존하게 되는 경우를 고려하는 건가
- 런타임에 이런 작업을 해야하는 이유: 유연성 + 확장성
  - 컴파일 시점에서 할 수 없는 클래스를 다룰 때 유용합니다.
  - 플러그인 아키텍처나 모듈 시스템을 구현할 때 필요합니다.
  - 프레임워크 개발
    - Spring과 같은 프레임워크는 런타임에 사용자 정의 클래스를 분석하고 객체를 생성해야합니다.
  - 테스트 도구
    - 단위 테스트 프레임워크에서 private 메소드나 필드에 접근할 때 사용됩니다.
  - 직렬화/역직렬화
    - JSON파싱 라이브러리 등에서 객체의 구조를 동적으로 분석하고 데이터를 매핑할 때 사용됩니다.
    -> 어떤식으로 하는거지
- 다형성과의 관계
  - 인터페이스에 의존하다가 런타임에 구체 클래스에 접근해야 하는 경우, 리플렉션이 유용할 수 있습니다.
  - 예를들어 팩토리 패턴에서 런타임에 동적으로 클래스를 결정하고 인스턴스를 생성할 때 사용할 수 있습니다.
- 구체적인 사용 예
  - 의존성 주입
    - 스프링 프레임워크는 리플렉션을 사용하여 런타임에 객체 의존성을 주입합니다.
  - ORM
    - Hibernate와 같은 ORM 도구는 리플렉션을 사용하여 자바 객체와 데이터베이스 테이블을 매핑합니다.
  - 어노테이션 처리
    - 런타임에 어노테이션 정보를 읽고 그에 따른 동작을 수행할 때 사용됩니다.
  - 동적 프록시
    - AOP에서 메소드 호출을 가로채고 추가 로직을 삽입할 때 사용됩니다.
- 간단한 구현 예시
```java
public class SimpleInjector {

    public <T> T inject(Class<T> clazz) throws Exception {
        // 주입하려는 클래스의 인스턴스를 생성합니다.
        T instance = clazz.getDeclaredConstructor().newInstance();

        // 클래스의 모든 필드를 가져옵니다.
        Field[] fields = clazz.getDeclaredFields();

        for (Field field : fields) {
            // @Autowired 어노테이션이 붙은 필드를 찾습니다.
            if (field.isAnnotationPresent(Autowired.class)) {
                // 필드의 접근 제어자가 private인 경우에도 접근 가능하게 합니다.
                field.setAccessible(true);

                // 필드의 타입을 가져옵니다.
                Class<?> fieldType = field.getType();

                // 필드 타입의 인스턴스를 생성합니다 (재귀적으로 의존성 주입).
                Object fieldInstance = inject(fieldType);

                // 생성된 인스턴스를 필드에 주입합니다.
                field.set(instance, fieldInstance);
            }
        }

        return instance;
    }
}

// 사용 예시
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}

    // 의존성 주입 실행
    SimpleInjector injector = new SimpleInjector();
    UserService userService = injector.inject(UserService.class);
    
//------------------------
public class SimpleInjector {

    public <T> T inject(Class<T> clazz) throws Exception {
        // 주입하려는 클래스의 인스턴스를 생성합니다.
        T instance = clazz.getDeclaredConstructor().newInstance();

        // 클래스의 모든 필드를 가져옵니다.
        Field[] fields = clazz.getDeclaredFields();

        for (Field field : fields) {
            // @Autowired 어노테이션이 붙은 필드를 찾습니다.
            if (field.isAnnotationPresent(Autowired.class)) {
                // 필드의 접근 제어자가 private인 경우에도 접근 가능하게 합니다.
                field.setAccessible(true);

                // 필드의 타입을 가져옵니다.
                Class<?> fieldType = field.getType();

                // 필드 타입의 인스턴스를 생성합니다 (재귀적으로 의존성 주입).
                Object fieldInstance = inject(fieldType);

                // 생성된 인스턴스를 필드에 주입합니다.
                field.set(instance, fieldInstance);
            }
        }

        return instance;
    }
}

// 사용 예시
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}

    // 의존성 주입 실행
    SimpleInjector injector = new SimpleInjector();
    UserService userService = injector.inject(UserService.class);
    
//----어노테이션 처리기
public class AnnotationProcessor {

    public void processAnnotations(Object obj) throws Exception {
        // 객체의 클래스를 가져옵니다.
        Class<?> clazz = obj.getClass();

        // 클래스의 모든 메소드를 가져옵니다.
        Method[] methods = clazz.getDeclaredMethods();

        for (Method method : methods) {
            // @LogExecutionTime 어노테이션이 붙은 메소드를 찾습니다.
            if (method.isAnnotationPresent(LogExecutionTime.class)) {
                // 메소드의 접근 제어자가 private인 경우에도 접근 가능하게 합니다.
                method.setAccessible(true);

                // 메소드 실행 전 시간을 기록합니다.
                long startTime = System.currentTimeMillis();

                // 메소드를 실행합니다.
                method.invoke(obj);

                // 메소드 실행 후 시간을 기록합니다.
                long endTime = System.currentTimeMillis();

                // 실행 시간을 출력합니다.
                System.out.println(method.getName() + " 실행 시간: " + (endTime - startTime) + "ms");
            }
        }
    }
}

// 사용 예시
public class SomeService {
    @LogExecutionTime
    public void doSomething() {
        // 어떤 작업 수행
    }
}

    // 어노테이션 처리 실행
    AnnotationProcessor processor = new AnnotationProcessor();
    SomeService service = new SomeService();
processor.processAnnotations(service);
        service.doSomething();

public class MyAnnotationProcessor {

    public void processAnnotations(Object object) throws Exception {
        Class<?> clazz = object.getClass();
        for (Method method : clazz.getDeclaredMethods()) {
            if (method.isAnnotationPresent(MyAnnotation.class)) {
                MyAnnotation annotation = method.getAnnotation(MyAnnotation.class);
                // 어노테이션 정보를 사용하여 특정 동작 수행
                System.out.println("Processing method: " + method.getName());
                System.out.println("Annotation value: " + annotation.value());

                // 메소드 호출 예시
                method.setAccessible(true);
                method.invoke(object);
            }
        }
    }
}

// 사용 예시
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface MyAnnotation {
    String value();
}

class MyClass {
    @MyAnnotation("Hello")
    public void myMethod() {
        System.out.println("MyMethod executed");
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        MyAnnotationProcessor processor = new MyAnnotationProcessor();
        processor.processAnnotations(new MyClass());
    }
}       
//--------------- JSON 파싱-------------------------
import java.lang.reflect.Field;
        import java.util.HashMap;
        import java.util.Map;

public class SimpleJsonParser {

    // JSON 문자열을 Java 객체로 변환하는 메서드
    public <T> T parse(String json, Class<T> clazz) throws Exception {
        // JSON 문자열을 Map으로 변환 (실제 구현에서는 더 복잡한 파싱 로직이 필요합니다)
        Map<String, Object> jsonMap = parseJsonToMap(json);

        // clazz의 새 인스턴스를 생성합니다
        T instance = clazz.getDeclaredConstructor().newInstance();

        // clazz의 모든 필드를 가져옵니다
        Field[] fields = clazz.getDeclaredFields();

        for (Field field : fields) {
            // 필드의 접근을 가능하게 합니다 (private 필드에 접근하기 위해)
            field.setAccessible(true);

            // 필드 이름을 가져옵니다
            String fieldName = field.getName();

            // JSON에서 해당 필드 이름에 맞는 값을 가져옵니다
            Object value = jsonMap.get(fieldName);

            if (value != null) {
                // 필드의 타입을 확인하고 적절한 형변환을 수행합니다
                if (field.getType() == int.class) {
                    field.setInt(instance, ((Number)value).intValue());
                } else if (field.getType() == long.class) {
                    field.setLong(instance, ((Number)value).longValue());
                } else if (field.getType() == double.class) {
                    field.setDouble(instance, ((Number)value).doubleValue());
                } else if (field.getType() == boolean.class) {
                    field.setBoolean(instance, (Boolean)value);
                } else {
                    // String 및 기타 객체 타입의 경우
                    field.set(instance, value);
                }
            }
        }

        return instance;
    }

    // JSON 문자열을 Map으로 변환하는 간단한 메서드 (실제 구현은 더 복잡합니다)
    private Map<String, Object> parseJsonToMap(String json) {
        // 이 부분은 실제 JSON 파싱 로직으로 대체되어야 합니다
        // 여기서는 간단한 예시만 제공합니다
        Map<String, Object> map = new HashMap<>();
        map.put("name", "John Doe");
        map.put("age", 30);
        map.put("isStudent", false);
        return map;
    }

    // 테스트를 위한 메인 메서드
    public static void main(String[] args) throws Exception {
        String json = "{\"name\":\"John Doe\",\"age\":30,\"isStudent\":false}";
        SimpleJsonParser parser = new SimpleJsonParser();
        Person person = parser.parse(json, Person.class);
        System.out.println(person);
    }
}

class Person {
    private String name;
    private int age;
    private boolean isStudent;

    // getter와 setter 메서드는 생략했습니다

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + ", isStudent=" + isStudent + "}";
    }
}

import java.lang.reflect.Field;
        import java.util.HashMap;
        import java.util.Map;

public class SimpleJsonSerializer {

    // Java 객체를 JSON 문자열로 변환하는 메서드
    public String serialize(Object obj) throws IllegalAccessException {
        // 객체의 클래스 정보를 가져옵니다
        Class<?> clazz = obj.getClass();

        // 결과를 저장할 Map을 생성합니다
        Map<String, Object> jsonMap = new HashMap<>();

        // 클래스의 모든 필드를 가져옵니다
        Field[] fields = clazz.getDeclaredFields();

        for (Field field : fields) {
            // private 필드에 접근할 수 있도록 설정합니다
            field.setAccessible(true);

            // 필드 이름을 가져옵니다
            String fieldName = field.getName();

            // 필드의 값을 가져옵니다
            Object value = field.get(obj);

            // Map에 필드 이름과 값을 저장합니다
            jsonMap.put(fieldName, value);
        }

        // Map을 JSON 문자열로 변환합니다 (실제 구현에서는 더 복잡한 로직이 필요합니다)
        return mapToJsonString(jsonMap);
    }

    // Map을 JSON 문자열로 변환하는 간단한 메서드 (실제 구현은 더 복잡합니다)
    private String mapToJsonString(Map<String, Object> map) {
        StringBuilder json = new StringBuilder("{");
        for (Map.Entry<String, Object> entry : map.entrySet()) {
            if (json.length() > 1) {
                json.append(",");
            }
            json.append("\"").append(entry.getKey()).append("\":");
            if (entry.getValue() instanceof String) {
                json.append("\"").append(entry.getValue()).append("\"");
            } else {
                json.append(entry.getValue());
            }
        }
        json.append("}");
        return json.toString();
    }

    // 테스트를 위한 메인 메서드
    public static void main(String[] args) throws IllegalAccessException {
        Person person = new Person("John Doe", 30, false);
        SimpleJsonSerializer serializer = new SimpleJsonSerializer();
        String json = serializer.serialize(person);
        System.out.println(json);
    }
}

class Person {
    private String name;
    private int age;
    private boolean isStudent;

    public Person(String name, int age, boolean isStudent) {
        this.name = name;
        this.age = age;
        this.isStudent = isStudent;
    }

    // getter와 setter 메서드는 생략했습니다
}
```
## 기본 동작 원리
- 모든 자바 클래스는 메모리에 로드될 때 자동으로 java.lang.Class 타입의 객체를 생성합니다.
- 이 Class 객체는 해당 클래스의 모든 정보(필드, 메서드, 생성자 등)을 포함합니다.
- 리플렉션 API는 이 Class 객체를 사용하여 클래스의 내부 정보에 접근합니다.

-> 요약하자면 메모리에 올라갈 때 Class 타입의 객체가 자동으로 생성되는데 여기에는 클래스의 모든 정보가 들어있다.
리플렉션은 이 모든 정보가 들어있는 Class 객체로부터 메모리에 올라간 클래스 정보에 접근을 한다는 거네<br>
그런데 이 Class 객체는 왜 생기는거지? 리플렉션을 위해서 생기는건가

- Class 객체의 목적
  - 타입 정보 제공
    - Java의 타입 시스템을 구현하는 데 필수적
    - instanceof연산자나 타입 캐스팅의 기반
  - 클래스 로딩
    - JVM이 클래스를 동적으로 로드하고 관리하는 데 사용됩니다.
  - 객체 생성
    - new 키워드를 사용한 객체 생성 시 Class 객체의 정보를 활용합니다.
  - 리플렉션
    - 런타임에 클래스의 구조를 분석하고 조작하는 데 사용됩니다.
  - 보안
    - Java 보안 매니저가 접근 권한을 확인할 때 Class 객체의 정보를 사용합니다.
  - 직렬화
    - 객체의 직렬화/역직렬화 과정에서 Class 객체의 정보가 사용됩니다.
- Class 객체의 생성 시점
  - 클래스가 처음 사용될 때(예: 객체의 생성, 정적 멤버 접근 등)
  - 클래스의 Class.forName() 메서드가 호출될 때
  - 해당 클래스의 서브클래스가 로드될 때
- 메모리 관리
  - Class 객체는 JVM의 메서드 영역(Java 8 이후 Metaspace라고도 한다)에 저장됩니다.
  - 클래스 로더가 언로드되지 않는 한, Class 객체는 GC의 대상이 되지 않습니다.

## 사용 사례
- 프레임워크 개발: Spring, Hibernate 등의 프레임워크가 객체의 프로퍼티에 동적으로 접근할 때
- 단위 테스트: private 메서드나 필드를 테스트할 때 활용
- 플러그인 시스템: 동적으로 클래스를 로드하고 인스턴스화할 때 사용

## 로우레벨 동작 원리
- 클래스 로딩: JVM이 클래스를 로드할 때, 해당 클래스의 바이트코드를 읽어 메모리에 적재합니다.
- Class 객체 생성: 클래스 로딩 시 java.lang.Class 객체가 생성되며, 이 객체는 PermGen 또는 Metaspace에 저장됩니다.
- 메타데이터 분석: Class 객체는 클래스의 메타데이터(필드, 메서드, 어노테이션 등)을 파싱하여 내부 데이터 구조에 저장합니다.

-> 데이터 구조에 저장한다는데 어떤 데이터 구조에 저장할까
```java
Class<?> clazz = MyClass.class;
System.out.println("Class name: " + clazz.getName());

Method method = clazz.getDeclareMethod("myMethod", String.class);
method.setAccessible(true); // private 메서드 접근 허용
method.invoke(object, "parameter");

Field field = clazz.getDeclareFeild("myField");
field.setAccessible(true);
Object value = field.get(object);

Constructor<?> constructor = clazz.getDeclaredConstruct();
Object instance = constructor.newInstance();
```

## 성능 고려사항
리플렉션은 강력하지만, 일반적인 메서드 호출이나 필드 접근보다 느립니다.
- 타입 검사가 런타임에 수행됨
- JVM 최적화가 제한적임
- 보안 검사가 필요함

## 보안
리플렉션은 private 멤버에도 접근할 수 있어 보안 위험이 있습니다. 이를 제어하기 위해 SecurityManager를 사용할 수 있습니다.
-> SecurityManager가 뭔데
- SecurityManager는 자바의 보안 기능 중 하나로 애플리케이션의 다양한 작업에 대한 접근 제어를 담당합니다.
- 잠재적으로 위험한 작업(파일 접근, 네트워크 연결 등)에 대한 권한을 검사합니다.
- 주요 기능
  - 파일 시스템 접근 제어
  - 네트워크 접근 제어
  - 클래스 로딩 제어
  - 스레드 생성 및 조작 제어
  - 시스템 속성 접근 제어
  - 리플렉션 사용 제어
- 동작 방식
  - JVM은 보안 검사가 필요한 작업을 수행하기 전에 SecurityManager의 해당 메서드를 호출하빈다.
  - SecurityManager는 작업의 허용 여부를 결정하고, 허용되지 않는 경우 SecurityException을 발생시킵니다.
- 참고로 Java 17부터는 deprecated 됐고 다른 보안 매커니즘으로 대체되고 있습니다.
## 리플렉션 내부 동작
우선 JVM의 동작 방식을 알아야 합니다.
- 클래스 로딩
  - 클래스가 처음 사용될 때, ClassLoader가 해당 클래스의 .class 파일을 찾아 바이트코드를 로드합니다.
  - 이 바이트코드는 메모리의 특정 영역(Method Area)에 저장됩니다.
- 링킹
  - verification: 바이트코드가 JVM 스펙에 맞는지 검증합니다.
  - preparation: 클래스 변수(static fields)를 위한 메모리를 할당하고 기본값으로 초기화합니다.
  - resolution: 심볼릭 레퍼런스를 직접 레퍼런스로 변환합니다.
- 초기화
  - 클래스의 정적 초기화 블록을 실행하고, 정적 필드를 선언된 값으로 초기화합니다.

-> 심볼릭 레퍼런스가 뭐지? 그리고 왜 기본값으로 초기화 한 다음 선언된 값으로 초기화하는걸까
- 심볼릭 레퍼런스
  - 심볼릭 레퍼런스는 클래스, 메서드, 필드 등을 가리키는 문자열 기반의 참조입니다.
  - 컴파일 시점에 실제 메모리 주소를 알 수 없기 때문에 사용됩니다.
  - 예를 들어 "java/lang/String.length()I" = String 클래스의 length()메서드를 가리키는 심볼릭 레퍼런스
  - 동작 과정
    - 컴파일러가 .class 파일을 생성할 때 심볼릭 레퍼런스를 포함합니다.
    - JVM이 클래스를 로드하고 링크하는 과정에서 심볼릭 레퍼런스를 실제 메모리 주소로 해석합니다.
    - 이 과정을 resolution이라고 합니다.
  - 장점
    - 유연성: 실제 메모리 레이아웃에 독립적인 코드 생성 가능
    - 동적 로딩: 필요할 때 클래스를 로드하고 링크할 수 있음
- 클래스 변수 초기화 과정
  - 초기화 과정이 두 단계로 나뉘는 이유는 JVM 명세와 관련이 있습니다.
  - 초기화 과정
    - 준비 단계: 클래스 변수를 기본값으로 초기화
    - 초기화 단계: 클래스 변수를 선언된 값으로 초기화
  - 나누는 이유
    - 일관성과 예측 가능성
      - 모든 클래스 변수가 먼저 알려진 상태(기본값)로 초기화되어 일관성을 유지합니다.
      - 초기화 순서에 따른 예기치 않은 동작을 방지합니다.
    - 순환 의존성 해결
      - 클래스 간 순환 의존성이 있을 때, 한 클래스의 초기화가 다른 클래스의 초기화에 의존할 수 있습니다.
      - 기본값 초기화로 이러한 순환 의존성 문제를 해결할 수 있습니다.
    - 성능 최적화
      - JVM은 준비 단계에서 메모리 할당과 기본값 초기화를 효율적으로 수행할 수 있습니다.
      - 복잡한 초기화 로직은 별도의 단계에서 처리됩니다.
    - 명확한 단계 구분
      - 클래스 로딩의 각 단계(로딩, 링킹, 초기화)를 명확히 구분할 수 있습니다.
      - 이는 JVM 구현과 디버깅을 용이하게 합니다.
## Class 객체의 구조
- 클래스 이름, 패키지 정보
- 수퍼 클래스와 구현한 인터페이스 정보
- 필드, 메서드, 생성자 정보
- 어노테이션 정보

이 정보들은 native 메서드를 통해 JVM 내부의 데이터 구조에서 가져옵니다.
-> native 메서드가 뭔데
- Java 코드로 작성되지 않고 다른 프로그래밍 언어(주로 C 또는 C++)로 작성된 메서드입니다.
- Java Native Interface(JNI)를 통해 Java 코드와 연결됩니다.
- 특징
  - native 키워드로 선언됩니다.
  - 메서드 body가 Java 코드로 구현되지 않습니다.
  - 구현은 별도의 네이티브 라이브러리에 있습니다.
- 사용 이유
  - 하드웨어나 운영 체제의 저수준 기능에 접근할 떄
  - 성능이 중요한 연산을 수행할 때
  - 기존의 C/C++ 라이브러리를 재사용할 때
  - JVM의 내부 구조에 접근해야 할 때(예: 리플렉션)
- 동작 원리
  - Java 코드에서 native 메서드 호출 -> JNI가 해당 네이티브 구현을 찾아 호출 -> 네이티브 코드 실행 -> 결과를 Java 코드로 변환
## 리플렉션과 제네릭
제네릭 타입 정보는 컴파일 시 소거되지만, 리플렉션을 통해 런타임에 일부 정보를 얻을 수 있습니다.
```java
Method method = MyClass.class.getMethod("myMethod");
Type returnType = method.getGenericReturnType();
if (returnType instanceof ParameterizedType) {
    Type[] typeArguments = ((ParameterizedType) returnType).getActualTypeArguments();
        
}
```

## 동적 프록시
런타임에 인터페이스 구현체를 동적으로 생성할 수 있습니다.
```java
InvocationHandler handler = new MyInvocationHandler();
MyInterface proxy = (MyInterface) Proxy.newProxyInstance(
    MyInterface.class.getClassLoader(),
    new Class<?>[] {MyInterface.class},
    handler    
);
```

## 리플렉션과 어노테이션
리플렉션을 통해 런타임에 어노테이션 정보를 조회하고 처리할 수 있습니다.
```java
Method method = MyClass.class.getMethod("myMethod");
if (method.isAnnotationPresent(MyAnnotation.class)) {
    MyAnnotation annotation = method.getAnnotation(MyAnnotation.class);    
}
```

## 리플렉션의 한계
- 컴파일 시간 타입 체크 불가능
- 리플렉션으로 접근한 private 멤버는 IDE의 리팩토링 도구가 추적하기 어려움
- 코드의 가독성과 유지보수성 저하 가능성

## 리플렉션과 JIT 컴파일러
JIT(Just-In-Time) 컴파일러는 리플레션 호출을 최적화하기 어렵습니다. 이는 리플렉션이 동적이어서 컴파일 시점에 최적화 대상을 특정하기 어렵기 떄문입니다.

## 리플렉션과 보안
리플렉션은 Java Security Manager의 영향을 받습니다. Security Mangager가 활성화된 환경에서는 리플렉션 작업에 대한 권한 검사가 수앻됩니다.
