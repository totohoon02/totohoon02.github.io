# EntityManager

- 영속성 컨텍스트와 상호작용으로 엔티티를 관리

### Persistence Context

- 엔티티를 저장하고 관리하는 컨텍스트
- 1차 캐싱, 쓰기 지연, 변경 감지를 통해 영속 로직 효율화

### 엔티티의 상태

- 비영속
  - 엔티티 객체가 생성되었으나 영속성 컨텍스트와 연관되지 않은 상태
  - 객체가 메모리 상에만 존재
  ```java
  Member member = new Member("MyMember");
  ```
- 영속
  - 영속성 컨텍스트에 의해 관리되는 상태
  - 변경 사항이 자동으로 데이터 베이스에 반영
  ```java
  Member member = new Member("MyMember");
  em.persist(member);
  ```
- 준영속
  - 영속성 컨텍스트에서 분리된 상태
  - 변경 사항이 데이터 베이스에 반영되지 않음
  ```java
  em.detach(member);
  ```
- 삭제
  - 영속성 컨텍스트에서 제거된 상태
  - 데이터 베이스에서 삭제
  ```java
  em.remove(member);
  ```
