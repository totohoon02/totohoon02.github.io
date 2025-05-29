# 함수형 프로그래밍 (Functional Programming)

함수형 프로그래밍은 프로그램을 순수 함수(pure function)의 조합으로 구성하는 프로그래밍 패러다임입니다. 이 접근 방식은 상태 변경과 가변 데이터를 피하고, 함수를 일급 객체(first-class citizen)로 취급합니다.

## 함수형 프로그래밍의 핵심 개념

1. **순수 함수(Pure Functions)**: 동일한 입력에 대해 항상 동일한 출력을 반환하며, 부작용(side effect)이 없는 함수
2. **불변성(Immutability)**: 데이터가 생성된 후 변경되지 않음
3. **고차 함수(Higher-Order Functions)**: 함수를 인자로 받거나 함수를 반환하는 함수
4. **함수 합성(Function Composition)**: 여러 함수를 조합하여 새로운 함수 생성
5. **재귀(Recursion)**: 반복문 대신 재귀를 사용하여 반복 작업 수행

## 파이썬에서의 함수형 프로그래밍 예시

### 1. 순수 함수 예시

```python
# 순수 함수 예시
def add(x, y):
    return x + y

# 순수하지 않은 함수 예시
total = 0
def add_to_total(x):
    global total
    total += x  # 전역 변수 변경 - 부작용 발생
    return total
```

### 2. 불변성 활용

```python
# 불변성 예시
def add_to_list(lst, item):
    # 원본 리스트를 변경하지 않고 새 리스트 반환
    return lst + [item]

original = [1, 2, 3]
new_list = add_to_list(original, 4)
# original은 여전히 [1, 2, 3]
# new_list는 [1, 2, 3, 4]
```

### 3. 고차 함수 활용

```python
# 고차 함수 예시
def multiply_by(factor):
    # 함수를 반환하는 함수
    def multiply(number):
        return number * factor
    return multiply

double = multiply_by(2)
triple = multiply_by(3)

print(double(5))  # 10
print(triple(5))  # 15
```

### 4. map, filter, reduce 활용

```python
from functools import reduce

# 리스트의 모든 요소에 함수 적용
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))
# [1, 4, 9, 16, 25]

# 조건에 맞는 요소만 필터링
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
# [2, 4]

# 요소들을 누적하여 하나의 값으로 축소
sum_of_numbers = reduce(lambda x, y: x + y, numbers)
# 15
```

### 5. 함수 합성

```python
def compose(f, g):
    return lambda x: f(g(x))

# 함수 정의
def double(x):
    return x * 2

def increment(x):
    return x + 1

# 함수 합성
double_then_increment = compose(increment, double)
increment_then_double = compose(double, increment)

print(double_then_increment(5))  # 11 (5*2 + 1)
print(increment_then_double(5))  # 12 ((5+1) * 2)
```

### 6. 리스트 컴프리헨션

```python
# 명령형 방식
squares = []
for i in range(10):
    squares.append(i**2)

# 함수형 방식 (리스트 컴프리헨션)
squares = [i**2 for i in range(10)]

# 필터링 추가
even_squares = [i**2 for i in range(10) if i % 2 == 0]
```

### 7. 재귀를 활용한 팩토리얼 계산

```python
def factorial(n):
    if n == 0 or n == 1:
        return 1
    else:
        return n * factorial(n-1)

print(factorial(5))  # 120
```

## 게임 개발에서의 함수형 프로그래밍 활용 예시

게임 개발에서 함수형 프로그래밍은 상태 관리, 이벤트 처리, AI 로직 등에 유용합니다.

```python
# 게임 엔티티의 상태 변경을 함수형으로 처리
def apply_damage(entity, damage):
    # 원본 엔티티를 변경하지 않고 새 엔티티 반환
    return {
        **entity,
        'health': max(0, entity['health'] - damage)
    }

def heal_entity(entity, amount):
    return {
        **entity,
        'health': min(entity['max_health'], entity['health'] + amount)
    }

# 사용 예시
player = {'name': 'Hero', 'health': 80, 'max_health': 100}
damaged_player = apply_damage(player, 20)
# player는 변경되지 않음
# damaged_player는 {'name': 'Hero', 'health': 60, 'max_health': 100}

healed_player = heal_entity(damaged_player, 30)
# healed_player는 {'name': 'Hero', 'health': 90, 'max_health': 100}
```

## 함수형 프로그래밍의 장점

1. **코드 가독성 향상**: 작은 순수 함수들로 구성되어 이해하기 쉬움
2. **테스트 용이성**: 순수 함수는 입출력만 테스트하면 되므로 단위 테스트가 간단
3. **병렬 처리 용이**: 부작용이 없어 병렬 실행이 안전함
4. **버그 감소**: 상태 변경이 적어 예측하지 못한 부작용으로 인한 버그 감소
5. **코드 재사용성 향상**: 작은 함수들을 조합하여 다양한 상황에 재사용 가능

## 함수형 프로그래밍의 단점

1. **학습 곡선**: 명령형 프로그래밍에 익숙한 개발자들에게는 생소할 수 있음
2. **성능 오버헤드**: 불변성 유지를 위한 객체 복사가 필요해 메모리 사용량 증가 가능
3. **일부 문제에 비효율적**: 상태 변경이 자연스러운 문제에는 적합하지 않을 수 있음

함수형 프로그래밍은 모든 문제에 대한 만능 해결책이 아니지만, 적절히 활용하면 코드의 품질과 유지보수성을 크게 향상시킬 수 있습니다.
