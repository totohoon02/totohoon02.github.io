# Strategy Pattern

- 전략패턴은 행동을 캡슐화하여, 실행 시점에 필요한 행동을 자유롭게 바꿀 수 있도록 설계하는 디자인 패턴입니다.
- Transportation 인터페이스가 전략의 공통 행동(move())을 정의합니다.

```java
// 전략 인터페이스
public interface Transportation {
    void move();
}
```

- Bus와 Train 클래스는 각각의 이동 방식으로 Transportation을 구현해 서로 다른 행동을 제공합니다.

```java
// 버스 전략
public class Bus implements Transportation {
    @Override
    public void move() {
        System.out.println("버스로 이동합니다.");
    }
}

// 기차 전략
public class Train implements Transportation {
    @Override
    public void move() {
        System.out.println("기차로 이동합니다.");
    }
}
```

- Person 클래스는 전략 인터페이스를 필드로 가지고 있으며, 생성자나 setTransportation() 메서드를 통해 전략 객체를 주입받아 동작합니다.
- Person 객체는 travel() 메서드를 호출할 때, 현재 설정된 전략의 move()를 실행하여 행동을 수행합니다.

```java
// 컨텍스트 클래스
public class Person {
    private Transportation transportation; // A는 B 이다 -> A에는 B가 있다

    // 생성자에서 전략(교통수단) 주입
    public Person(Transportation transportation) {
        this.transportation = transportation;
    }

    // 전략 교체 메서드
    public void setTransportation(Transportation transportation) {
        this.transportation = transportation;
    }

    // 현재 전략에 따라 이동 실행
    public void travel() {
        transportation.move();
    }
}
```

- 이를 통해 조건문 없이도 런타임에 행동을 바꿀 수 있어, 코드의 유연성과 유지보수성이 크게 향상됩니다.

```java
// 사용 예시
public class Main {
    public static void main(String[] args) {
        // 초기 전략: 버스
        Person john = new Person(new Bus());
        john.travel();  // 출력: 버스로 이동합니다.

        // 전략 변경: 기차
        john.setTransportation(new Train());
        john.travel();  // 출력: 기차로 이동합니다.
    }
}
```
