# 동일성(Identity) vs 동등성(Equality)

객체를 비교할 때, **동일성(Identity)** 과 **동등성(Equality)** 은 중요한 개념입니다. 자바에서는 `==` 연산자와 `equals()` 메서드를 통해 이 두 개념을 구분합니다.

---

## 1. 동일성 (Identity)

- **정의**: 두 변수가 **같은 객체**(메모리 상 동일한 인스턴스)를 가리키는지를 비교
- **연산자**: `==`
- **예시**:
  ```java
  public static void main(String[] args) {
      Apple apple1 = new Apple(100);
      Apple apple2 = new Apple(100);
      Apple apple3 = apple1;

      System.out.println(apple1 == apple2); // false
      System.out.println(apple1 == apple3); // true
  }

- `apple1`과 `apple2`는 서로 다른 인스턴스이므로 `false`
- `apple3`는 `apple1`이 가리키는 동일한 객체를 참조하므로 `true`

---

## 2. 동등성 (Equality)

* **정의**: 두 객체의 **내용**이 같은지를 논리적으로 비교
* **메서드**: `equals(Object obj)`
* **기본 동작**: `Object` 클래스에서는 `equals()`가 `==` 연산과 같음

  ```java
  public class Object {
      public boolean equals(Object obj) {
          return (this == obj);
      }
  }
  ```
* **오버라이딩 필요**: 값 기반 비교를 위해서는 클래스에서 `equals()`와 `hashCode()`를 적절히 오버라이딩해야 함

### Apple 클래스 예시

```java
public class Apple {
    private final int weight;

    public Apple(int weight) {
        this.weight = weight;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Apple apple = (Apple) o;
        return weight == apple.weight;
    }

    @Override
    public int hashCode() {
        return Objects.hash(weight);
    }

    public static void main(String[] args) {
        Apple apple = new Apple(100);
        Apple anotherApple = new Apple(100);

        System.out.println(apple.equals(anotherApple)); // true
    }
}
```

* 두 객체의 `weight` 값이 같으므로 `equals()` 결과 **true**

---

## 3. 문자열(String) 비교

* 문자열 리터럴은 **문자열 상수 풀(String Constant Pool)** 에 저장되어 동일한 리터럴은 같은 객체 참조
* **예시**:

  ```java
  String str1 = "안녕하세요";
  String str2 = "안녕하세요";
  String str3 = new String("안녕하세요");

  System.out.println(str1 == str2);    // true  (풀에서 같은 객체)
  System.out.println(str1 == str3);    // false (새로운 인스턴스)

  System.out.println(str1.equals(str2)); // true
  System.out.println(str1.equals(str3)); // true
  ```
* 항상 내용 비교를 위해 **`equals()`** 사용 권장

---

## 4. 래퍼 클래스(Integer 등) 비교

* `Integer`, `Long` 등 래퍼 클래스도 객체이므로 `==`는 참조 비교
* 자바는 **-128\~127** 범위 값을 캐싱하므로 이 범위 내에서는 `==` 가 **true** 일 수 있음
* 일반적으로 **값 비교를 위해 `equals()` 사용**

  ```java
  Integer a = 127;
  Integer b = 127;
  System.out.println(a == b);       // true

  Integer x = 128;
  Integer y = 128;
  System.out.println(x == y);       // false

  System.out.println(x.equals(y)); // true
  ```

---

## 5. 요약

| 개념  | 연산/메서드           | 비교 대상    | 반환 예시                                                 |
| --- | ---------------- | -------- | ----------------------------------------------------- |
| 동일성 | `==`             | 참조(레퍼런스) | `apple1 == apple2` → false, `apple1 == apple3` → true |
| 동등성 | `equals(Object)` | 객체 내용    | `apple.equals(anotherApple)` → true                   |

* **동일성**은 **메모리 주소**(참조) 비교
* **동등성**은 **논리적/내용적** 비교
* 사용자 정의 클래스에서 동등성 비교를 위해 `equals()`와 `hashCode()` **반드시** 오버라이딩해야 함
