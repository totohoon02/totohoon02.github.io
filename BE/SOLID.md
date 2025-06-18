# SOLID

### S

- SRP - Single Response
    - 하나의 클래스는 하나의 책임을 가진다.
    - 슈퍼 클래스x

### O

- OCP - Open Close
    - 기존의 코드를 변경하지 않고 기능 추가
    - 인터페이스 기반 구현

### L

- LSP - Liskov Substituion
    - 부모의 규약을 자식이 따라야 한다.
    - forward()는 forward의 기능을 해야 한다.

### I

- ISP - Interface Segregation
    - 인터페이스를 용도에 맞게 각기 분리
    - 슈퍼 인터페이스x

### D

- DIP - Dependency Inversion
    - 구체가 아니라 추상에 의존
    - 구현체가 여러 개 일 때 구체가 아니라 추상에 의존하면 클라이언트의 수정 불필요
