# 자바 정적 클래스와 내부 클래스 비교

## 정적 클래스 (Static Class)

**특징:**

- 외부 클래스의 정적 멤버로 선언됨
- static 키워드를 사용하여 선언
- 외부 클래스의 인스턴스 없이도 사용 가능
- 외부 클래스의 인스턴스 변수에 직접 접근할 수 없음
- 외부 클래스의 정적 멤버에만 직접 접근 가능

**사용 이유:**

- 논리적으로 그룹화가 필요한 클래스를 표현할 때
- 특정 클래스가 다른 클래스에서만 사용될 때
- 외부 클래스와 도우미 관계를 가질 때
- 외부 클래스의 상태와 독립적인 동작이 필요할 때
- 메모리 효율성: 외부 클래스의 인스턴스와 연결되지 않음

## 내부 클래스 (Inner Class)

**특징:**

- 외부 클래스의 인스턴스 멤버로 선언됨
- static 키워드 없이 선언
- 외부 클래스의 인스턴스 생성 후에만 사용 가능
- 외부 클래스의 인스턴스 변수와 메소드에 직접 접근 가능
- 외부 클래스와 강하게 결합됨

**사용 이유:**

- 외부 클래스의 인스턴스 상태에 의존하는 작업을 수행할 때
- 특정 객체의 내부 동작을 캡슐화할 때
- 외부 클래스의 멤버에 쉽게 접근해야 할 때
- 이벤트 핸들러나 콜백 구현에 유용
- UI 컴포넌트의 동작을 구현할 때

## 코드 예시

```java
public class OuterClass {
    private int outerValue = 10;
    private static int staticValue = 20;

    // 정적 클래스
    public static class StaticNestedClass {
        public void display() {
            // 외부 클래스의 정적 변수에만 접근 가능
            System.out.println("Static value: " + staticValue);

            // 오류 발생: 인스턴스 변수에 접근할 수 없음
            // System.out.println(outerValue);
        }
    }

    // 내부 클래스
    public class InnerClass {
        public void display() {
            // 외부 클래스의 모든 멤버에 접근 가능
            System.out.println("Outer value: " + outerValue);
            System.out.println("Static value: " + staticValue);
        }
    }

    public static void main(String[] args) {
        // 정적 클래스 인스턴스 생성
        StaticNestedClass staticNested = new StaticNestedClass();
        staticNested.display();

        // 내부 클래스 인스턴스 생성을 위해서는 외부 클래스의 인스턴스가 필요
        OuterClass outer = new OuterClass();
        InnerClass inner = outer.new InnerClass();
        inner.display();
    }
}
```

## 선택 기준

- **정적 클래스 사용:** 외부 클래스의 인스턴스 상태와 무관하게 동작해야 하는 경우
- **내부 클래스 사용:** 외부 클래스의 인스턴스와 강하게 연결되고 그 멤버에 접근해야 하는 경우

두 방식 모두 코드 구조화와 캡슐화에 유용하지만, 사용 사례에 따라 적절한 것을 선택해야 합니다.
