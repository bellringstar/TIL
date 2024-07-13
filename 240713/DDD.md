# DDD (Domain-Driven Design)
복잡한 소프트웨어 시스템을 도메인 모델을 중심으로 설계하고 개발하는 방법론입니다. 
DDD는 도메인 전문가와 개발자 간의 협업을 통해 도메인 지식을 명확히하고, 이를 기반으로 소프트웨어를 설계함으로써 복잡성을 효과적으로 관리하는 것을 목표로 합니다.

## DDD의 핵심 개념
1. 도메인
   - 소프트웨어 시스템이 해결하려는 문제 영역입니다. 예를 들어, 전자상거래 시스테므이 도메인은 주문, 결제, 배송 등입니다.
2. 도메인 모델
   - 도메인을 나타내는 개념적 모델로, 도메인의 개념과 그들 간의 관계를 정의합니다. 도메인 모델은 객체지향 모델링을 사용하여 구현됩니다.
3. 유비쿼터스 언어
   - 도메인 전문가와 개발자가 공통적으로 사용하는 언어입니다. 유비쿼터스 언어는 도메인 모델을 표현하는데 사용되며, 도메인 전문가와 개발자 간의 의사소통을 원활하게 합니다.
4. 경계 컨텍스트
   - 도메인 모델의 경계를 정의하는 개념입니다. 각 경계 컨텍스트는 독립적인 모델을 가지며, 시스템의 일부분을 나타냅니다. 예를 들어, 전자상거래 시스템에서 주문과 결게는 각각의 경계 컨텍스트를 가질 수 있습니다.

## DDD의 주요 구성 요소
1. 엔티티 (Entity)
   - 고유한 식별자를 가지며, 지속적인 상태를 유지하는 객체입니다. 예를 들어, 주문 엔티티는 주문 ID를 고유한 식별자로 가질 수 있습니다.
2. 밸류 오브젝트 (Value Object)
   - 식별자가 없고, 값으로만 구분되는 객체입니다. 불변성을 가지며, 복합 값을 나타낼 때 사용됩니다. 예를 들어, 주소는 벨류 모브젝트로 표현될 수 있습니다.
   - DTO랑 비슷?
3. 애그리게이트 (Aggregate)
   - 엔티티와 밸류 오브젝트의 집합으로, 트랜잭션의 일관성을 유지하기 위한 경계를 정의합니다. 애그리게이트는 루트 엔티티(Aggregate Root)를 통해 접근됩니다.
4. 리포지토리 (Repository)
   - 엔티티와 애그리게이트를 저장하고 검색하는 인터페이스입니다. 데이터 저장소와 도메인 모델 간의 추상화를 제공합니다.
5. 서비스 (Service)
   - 도메인 로직을 포함하지 않는 객체로, 특정 도메인 기능을 제공하는 데 사용됩니다. 도메인 서비스는 여러 엔티티와 밸류 오브젝트를 조작하는 로직을 포함할 수 있습니다.
6. 팩토리 (Factory)
   - 복잡한 객체 생성 로직을 캡슐화하여, 클라이언트 코드가 객체 생성 로직에 의존하지 않도록 합니다.

## DDD의 설계 패턴
1. 레이어드 아키텍처
   - 도메인 로직, 애플리케이션 로직, 사용자 인터페이스, 인프라스트럭처 등을 계층으로 분리하여 설계합니다. 각 계층은 명확한 책임을 가지며, 계층 간의 의존성을 최소화합니다.
2. 헥사고날 아키텍처
   - 포트와 어댑터 패턴을 사용하여, 도메인 로직을 외부 인터페이스와 분리합니다. 이를 통해 도메인 로직의 변경 없이 인터페이스를 쉽게 교체할 수 있습니다.
3. 이벤트 소싱
   - 상태 변경을 이벤트로 기록하고, 이벤트를 재생하여 현재 상태를 계산합니다. 이를 통해 데이터 변경 이력을 명확하게 추적할 수 있습니다.
4. CQRS(Command Query Responsibility Segregation)
   - 읽기 작업과 쓰기 작업을 분리하여, 서로 다른 모델을 사용합니다. 이는 성능을 최적화하고, 복잡성을 관리하는 데 도움을 줍니다.

## DDD 적용 예시
전자상거래 시스템의 예시
1. 도메인 모델링
   - 도메인 전문가와 협력하여 주문, 결제, 배송 등의 도메인 모델을 정의합니다.
   - 유비쿼터스 언어를 사용하여 도메인 개념과 그들 간의 관계를 명확히 합니다.
2. 경계 컨텍스트 정의
   - 주문, 결제, 배송 등을 각각의 경계 컨텍스트로 분리합니다.
3. 애그리게이트 설계
   - 주문 애그리게이트는 주문 엔티티와 주문 항목 밸류 오브젝트로 구성됩니다.
   - 주문 엔티티는 주문 ID를 식별자로 가지며, 주문 항목은 불변의 값으로 표현됩니다.
4. 리포지토리 구현
   - OrderRepository 인터페이스를 정의하고, 이를 구현하여 주문 애그리게이트를 저장하고 검색하는 기능을 제공합니다.

```java
//엔티티
public class Order {
    private Long id;
    private List<OrderItem> items;

    public Order(Long id) {
        this.id = id;
        this.items = new ArrayList<>();
    }

    public void addItem(OrderItem item) {
        this.items.add(item);
    }
}
// 밸류 오브젝트
public class OrderItem {
    private String productId;
    private int quantity;
    
    public OrderItem(String productId, int quantity) {
        this.productId = productId;
        this.quantity = quantity;
    }
}

// 리포지토리
public interface OrderRepository {
    void save(Order order);
    Order findById(Long id);
}

// 서비스
public class OrderService {
    private final OrderRepository orderRepository;

   public OrderService(OrderRepository orderRepository) {
       this.orderRepository = orderRepository;
   }

   public void createOrder(Long orderId, List<OrderItem> items) {
      Order order = new Order(orderId);
      for (OrderItem item : items) {
         order.addItem(item);
      }
      orderRepository.save(order);
   }
}
```