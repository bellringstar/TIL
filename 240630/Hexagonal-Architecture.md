# Hexagonal Architecture 유연하고 지속 가능한 소프트웨어 설계
## Hexagonal Architecture란?
Hexagonal Architecture는 소프트웨어 설계 패턴의 하나로, 포트와 어댑터 아키텍처라고도 불립니다. 
이 아키텍처는 애플리케이션의 핵심 로직을 외부 요소로부터 분리하여 유지보수성, 테스트 용이성 그리고 유연성을 높이는 것을 목표로 합니다.
## 왜 Hexagonal Architecture인가?
1. 관심사의 분리 : 비즈니스 로직과 외부 인터페이스를 명확히 구분합니다.
2. 테스트 용이성 : 핵심 로직을 외부 의존성 없이 쉽게 테스트할 수 있습니다.
3. 유연성 : 새로운 기술이나 인터페이스를 쉽게 추가하거나 변경할 수 있습니다.
4. 지속 가능성 : 시간이 지나도 코드베이스를 쉽게 유지보수 할 수 있습니다.
## Hexagonal Architecture의 주요 구성 요소
1. 도메인(Domain)
- 애플리케이션의 핵심 비즈니스 로직을 포함합니다.
- 외부 의존성이 없어야 합니다.
2. 포트(Ports)
- 애플리케이션과 외부 세계 사이의 인터페이스를 정의합니다.
- 입력 포트(Inbound) : 애플리케이션의 기능을 외부에 노출합니다.
- 출력 포트(Outbound) : 애플리케이션이 외부 서비스를 사용하는 방법을 정의합니다.
3. 어댑터(Adapters)
- 포트의 실제 구현을 제공합니다.
- 외부 시스템과 애플리케이션 사이의 변환 로직을 포함합니다.
## 예시로 이해하기
온라인 서점 애플리케이션을 Hexagonal Architecture로 설계한다고 가정해봅시다.
1. 도메인 로직
- Book, Order, Customer등의 핵심 엔티티
- 주문 처리, 재고 관리 등의 비즈니스 로직
2. 입력 포트(Inbound Ports)
- OrderService 인터페이스
```java
public interface OrderService {
    Order placeOrder(Customer customer, List<Book> books);
}
```
3. 출력 포트(Outbound Ports)
- BookRepository 인터페이스 : 책 정보 저장 및 조회
```java
public interface BookRepository {
    Book findById(String bookId);
    void save(Book book);
}
```
4. 입력 어댑터(Inbound Adapters)
- REST API 컨트롤러
```java
@RestController
public class OrderController {
    private final OrderService orderService;
    
    @PostMapping("/orders")
    public Order createOrder(@RequestBody OrderRequest request) {
        // orderService.placeOrder();
    }
}
```
5. 출력 어댑터(Outbound Adapters)
- 데이터베이스 구현
```java
@Repository
public class JpaBookRepository implements BookRepository {
    @Autowired JpaBookEntityRepository jpaRepository;
    
    @Override
    public Book findById(String bookId) {
        // 조회
    }
}
```
## 주요 용어
- 도메인 : 비즈니스 문제 영역과 그 해결책을 나타내는 개념들의 집합
- 포트 : 애플리케이션과 외부 세계 사이의 연결점을 정의하는 인터페이스
- 어탭터 : 포트 인터페이스의 실제 구현으로 외부 시스템과 통신을 담당
- 의존성 주입 : 외부에서 객체의 의존서을 제공하는 디자인 패턴으로 결합도를 낮추고 테스트 용이성을 높입니다.

## 면접 예상 질문
1. Hexagonal Architecture가 뭔지 간단히 설명해주실 수 있나요?
- Hexagonal Architecture는 애플리케이션의 핵심 비즈니르 로직을 외부 요소로부터 분리하는 소프트웨어 설계 방식입니다. 
이렇게 하는 이유는 핵심로직을 독립적으로 개발하고 테스트할 수 있게 만들어서, 장기적으로 유지보수가 쉽고 변경에 유연한 시스템을 만들기 위해섭니다.
 예를 들어, 데이터베이스나 UI가 변경되더라도 핵심 비즈니스 로직은 그대로 유지할 수 있게 됩니다.
2. Hexagonal Architecture의 주요 구성 요소에 대해 말씀해 주시겠어요?
- 크게 세 가지로 나눌 수 있습니다. 첫째로 도메인입니다. 이는 애플리케이션의 핵심 비즈니르 로직을 담고 있습니다.
 둘째로 포트입니다. 이는 애플리케이션과 외부 세계를 연결하는 인터페이스를 정의합니다. 마지막으로 어댑터가 있는데 
이는 포트의 실제 구현을 제공하고 외부 시스템과 통신을 담당합니다. 이렇게 구성하는 이유는 각 부분의 역할을 명확히 분리해서 변경이 필요할 때 
다른 부분에 영향을 최소화 하기 위해서입니다.
3. Hexagonal Architecture를 사용하면 어떤 이점이 있나요?
- 주요 이점으로는 네가지를 들 수 있습니다. 첫째, 관심사의 분리로 코드가 더 명확해집니다. 둘째 핵심 로직을 외부 의존성 없이 쉽게 테스트할 수 있습니다. 
셋째 새로운 기술이나 인터페이스를 쉽게 추가하거나 변경할 수 있는 유연성이 있습니다. 마지막으로 시간이 지나도 코드베이스를 쉽게 
유지보수할 수 있어 지속 가능한 개발이 가능해집니다. 이런 이점들 때문에 복잡한 비즈니스 로직을 가진 큰 규모의 애플리케이션 개발에 특히 유용합니다.
4. Hexagonal Architecture를 실제 프로젝트에 적용해 본 경험이 있나요?