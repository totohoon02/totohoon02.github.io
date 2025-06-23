# 일급 컬렉션 (First-Class Collection)

## 정의
- 하나의 컬렉션(List, Set 등)을 감싸는 클래스를 만들고,
- 해당 클래스에서 컬렉션 관련 비즈니스 로직을 관리하는 패턴

## 예시 코드
```java
public class Orders {

    private final List<Order> orders;

    public Orders(List<Order> orders) {
        validate(orders); // 검증 수행
        ...
    }

    public void add(Order order) {
        if (order == null) {
            throw new IllegalArgumentException("Order cannot be null");
        }
        orders.add(order);
    }

    public List<Order> getAll() {
        return Collections.unmodifiableList(orders);
    }

    public double getTotalAmount() {
        return orders.stream()
                     .mapToDouble(Order::getAmount)
                     .sum();
    }
}

public class OrderService {

    private final Orders orders = new Orders();

    public void addOrder(Order order) {
        orders.add(order);
    }

    public Orders getOrders() {
        return orders;
    }
}
```

### 장점

- 비즈니스 로직의 응집
  - 컬렉션과 관련된 로직을 해당 클래스에 모아서 관리 가능

- 명확한 의미 부여
  - 컬렉션에 비즈니스 특화된 이름을 부여해 코드 가독성 향상

- 외부 노출 제한
  - 불필요한 컬렉션 API(예: 직접 수정 등)를 외부에 노출하지 않음

- 데이터 무결성 보호
  - 컬렉션을 불변으로 만들거나 변경을 제한하여 예기치 않은 변경 방지

- 유지보수 용이
  - 컬렉션 관련 변경 시 해당 클래스 내에서 일괄 관리 가능

