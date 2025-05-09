# Java Wildcards (와일드카드)

자바의 와일드카드는 제네릭 타입을 더 유연하게 사용할 수 있게 해주는 특별한 타입 매개변수입니다.

## 1. 와일드카드의 종류

- Unbounded Wildcard (<?>) - 제한 없는 와일드카드
- Upper Bounded Wildcard (<? extends Type>) - 상한 제한 와일드카드
- Lower Bounded Wildcard (<? super Type>) - 하한 제한 와일드카드

## 2. Unbounded Wildcard (<?>) 사용 예시

```java
public void printList(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}
```

이 방식은 모든 타입의 List를 받아들일 수 있지만, 리스트에 요소를 추가할 수는 없습니다.

## 3. Upper Bounded Wildcard 사용 예시

```java
public double sumOfNumbers(List<? extends Number> numbers) {
    double sum = 0.0;
    for (Number num : numbers) {
        sum += num.doubleValue();
    }
    return sum;
}
```

Number 클래스나 그 하위 클래스(Integer, Double 등)의 리스트만 받아들입니다.

## 4. Lower Bounded Wildcard 사용 예시

```java
public void addIntegers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}
```

Integer 클래스나 그 상위 클래스(Number, Object)의 리스트에 Integer 객체를 추가할 수 있습니다.

## 5. 와일드카드 사용 지침

- PECS (Producer Extends, Consumer Super) 원칙
- 데이터를 읽기만 할 때는 <? extends T> 사용
- 데이터를 쓰기만 할 때는 <? super T> 사용

## 6. 주의사항

- 와일드카드는 메서드의 파라미터 타입으로만 사용
- 변수의 타입이나 리턴 타입으로는 사용하지 않음
- 제네릭 메서드를 사용할 수 있는 경우에는 와일드카드 대신 제네릭 메서드를 선호
