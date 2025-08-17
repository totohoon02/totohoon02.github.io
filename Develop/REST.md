
# REST와 RESTful API 정리

## 📌 REST(Representational State Transfer)

REST는 **자원의 표현을 이용하여 상태를 주고받는 아키텍처 스타일**입니다.  
여기서 **자원(Resource)** 은 소프트웨어가 관리하는 모든 것을 의미하며,  
**자원의 표현(Representation)** 은 자원을 나타내기 위한 이름을 의미합니다.

예를 들어, 서버가 관리하는 주문 데이터는 `order`라는 자원으로 표현할 수 있습니다.  
최근에는 자원의 상태를 나타내기 위해 **JSON 포맷**을 주로 사용합니다.

REST는 **HTTP 프로토콜**을 기반으로 동작하며,  
**HTTP URI**로 자원을 명시하고 **HTTP METHOD**를 통해 CRUD 연산을 수행합니다.

---

## 📌 API(Application Programming Interface)

API는 **컴퓨터 프로그램 간에 정보를 주고받을 수 있는 출입구** 역할을 합니다.  
API가 REST 원칙을 준수하여 구현된 경우 이를 **REST API** 또는 **RESTful API**라고 부릅니다.

---

## 📌 RESTful API

**RESTful API**는 다음 조건을 만족하는 API를 의미합니다.

1. **클라이언트-서버 구조**  
   - 클라이언트와 서버의 역할을 명확히 분리

2. **무상태성(Stateless)**  
   - 각 요청 간에 상태를 저장하지 않음 (모든 요청은 필요한 정보를 포함해야 함)

3. **캐시 가능(Cacheable)**  
   - HTTP의 캐싱 기능을 활용 가능

4. **계층 구조(Layered System)**  
   - API 서버, 로드 밸런서, 인증 서버 등을 계층적으로 배치 가능

5. **인터페이스 일관성(Uniform Interface)**  
   - 동일한 URI 구조와 응답 형식 유지

---

## 📌 HTTP 메서드와 CRUD 매핑

| HTTP Method | CRUD 동작 | 설명 |
|-------------|-----------|------|
| GET         | Read      | 자원 조회 |
| POST        | Create    | 자원 생성 |
| PUT         | Update    | 자원 전체 수정 |
| PATCH       | Update    | 자원 일부 수정 |
| DELETE      | Delete    | 자원 삭제 |

---

## 📌 RESTful API 예시

### 1. 주문 목록 조회
```http
GET /orders
```
응답 예시:
```json
[
  {
    "orderId": 1,
    "product": "Laptop",
    "quantity": 2
  },
  {
    "orderId": 2,
    "product": "Mouse",
    "quantity": 1
  }
]
```

### 2. 특정 주문 조회
```http
GET /orders/1
```
응답 예시:
```json
{
  "orderId": 1,
  "product": "Laptop",
  "quantity": 2
}
```

### 3. 주문 생성
```http
POST /orders
Content-Type: application/json

{
  "product": "Keyboard",
  "quantity": 1
}
```
응답 예시:
```json
{
  "orderId": 3,
  "product": "Keyboard",
  "quantity": 1
}
```

### 4. 주문 수정
```http
PUT /orders/1
Content-Type: application/json

{
  "product": "Laptop",
  "quantity": 5
}
```

### 5. 주문 삭제
```http
DELETE /orders/1
```

---

## 📌 REST의 장점

- **서버와 클라이언트 역할 분리**
- **플랫폼 독립성**: HTTP를 지원하는 모든 환경에서 사용 가능
- **간편한 테스트**: CURL, Postman 등으로 손쉽게 테스트 가능

---

## 📌 REST의 단점

- 요청/응답 스타일만 지원
- HTTP 메서드로 표현하기 어려운 경우 존재
- 한 번의 요청으로 여러 자원 가져오기 어려움
- JSON 같은 텍스트 포맷 사용 시 메시지가 길어 네트워크 트래픽 증가 가능

---

## 📌 JSON 포맷의 장점과 단점

**장점**
- 자기 서술적(self-descriptive) → 구조 변경 시 하위 호환성 용이
- 필요한 값만 선택적으로 사용 가능

**단점**
- 메시지가 길어 전송 속도 저하 가능
- 해석(Parsing) 과정에서 오버헤드 발생

---

✅ **정리**:  
RESTful API는 웹 서비스 설계의 표준 중 하나이며, 단순성과 확장성 측면에서 장점이 많지만,  
데이터 전송량과 복잡한 행위 표현의 어려움이 단점이 될 수 있습니다.
