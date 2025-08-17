# Database Indexing

-   테이블 검색 속도 향상
-   저장되는 컬럼의 값을 사용해 정렬된 상태 유지
-   `INSERT`, `UPDATE`, `DELETE`의 성능 희생

### 인덱스의 자료구조

-   B+Tree

    ```
          [Root Node]
            /    \
        [Branch Node]  [Branch Node]
          /      \        /     \
      [Leaf Node] [Leaf Node] [Leaf Node] [Leaf Node]
      (PK 또는) (PK 또는) (PK 또는) (PK 또는)
      (레코드 주소) (레코드 주소)(레코드 주소)(레코드 주소)
    ```

    -   루트 노드 - 시작점
    -   중간 노드 - 길 찾는 역할
    -   리프 노드 - 실제 데이터의 주소

-   프라이머리 인덱스
    -   리프 노드에 실제 데이터가 있음
    -   PK를 기준으로 정렬, 저장
    -   기본 테이블 구조
-   세컨더리 인덱스
    -   PK 외 다른 컬럼을 기준으로 정렬된 별도의 트리 생성
    -   리프 노드에 기본 키의 값을 있음
    -   세컨더리 인덱스의 정렬된 값에서 PK를 찾고 프라이머리 인덱스에서 다시 검색

```sql
-- 세컨더리 인덱스 생성 기본 문법
CREATE INDEX 인덱스명 ON 테이블명 (컬럼명);

-- 예시: users 테이블 email 컬럼에 세컨더리 인덱스 생성
CREATE INDEX idx_email ON users (email);

-- 여러 컬럼 복합 인덱스 생성도 가능
CREATE INDEX idx_name_email ON users (name, email);

```

### 인덱스 스캔 방식

-   인덱스 레인지 스캔
    -   특정한 범위 조건에 해당하는 값들만 검색
-   인덱스 풀 스캔
    -   처음부터 끝까지
-   루스 인덱스 스캔
    -   중복된 값 중 대표값만 빠르게 찾음

> 인덱싱이 없어도 where이 가능한데?

-   인덱싱이 없으면 전체 레코드에 대해서 검색 (O(N))
-   인덱싱이 있으면 트리구조로 검색 (O(logN))
-   속도의 차이가 있다.
