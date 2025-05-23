# SHA와 bcrypt, scrypt 특징 비교

## SHA(Secure Hash Algorithm)

- 목적: 데이터 무결성 검증, 디지털 서명 등 일반적인 해시 용도
- 속도: 매우 빠름 (빠른 계산을 위해 설계됨)
- 보안: 해시 경쟁에 취약함 (특히 SHA-1, SHA-2)
  - 서로 다른 입력 데이터가 동일한 해시 값 생성하는 현상
  - 고정된 길이의 해시 값을 생성하기 때문에 필연적으로 발생할 수 밖에 없다.
- 워크팩터: 일반적으로 낮음 (빠른 계산을 위해 설계됨)
  - 알고리즘의 계산 복잡도
- 솔팅: 기본적으로 포함하지 않음 (수동으로 구현해야 함)
  - salting임 ㅋㅋㅋㅋ
- 출력 길이: 버전에 따라 다름 (SHA-256: 256비트, SHA-512: 512비트 등)
  - 길이에 따라 버전 구분

## SHA 알고리즘의 특징

- 단방향성: 해시값에서 원본 메시지를 복원할 수 없음
- 결정성: 동일한 입력에 대해 항상 동일한 해시값 생성
- 눈사태 효과: 입력의 작은 변화도 출력 해시값의 큰 변화 초래
- 충돌 저항성: 동일한 해시값을 생성하는 서로 다른 입력을 찾기 어려움

## bcrypt

- 목적: 패스워드 해시용으로 특화된 알고리즘
- 속도: 의도적으로 느림 (브루트포스 공격 방지를 위해)
- 보안: 고도로 보안적 (브루트포스, 레인보우 테이블 공격에 강함)
  - 레인보우 테이블
    - 미리 값 - 해시를 계산해둔 테이블
    - 해시 값으로 값을 역산
    - 솔트를 더하거나 계산 비용이 높은 해시 값으로 대처
- 워크팩터: 조절 가능 (비용 인자로 설정 가능, 일반적으로 10~12 사용)
- 솔팅: 기본적으로 포함 (자동 솔팅 적용)
- 출력 길이: 고정 (60자 문자열)

## **bcrypt 알고리즘의 특징**

- 패스워드 특화: 비밀번호 저장을 위해 특별히 설계됨
- 조절 가능한 비용: 워크팩터를 통해 계산 복잡성 조절 가능
- 내장 솔팅: 자동으로 솔트 생성 및 적용
- 고정 출력 길이: 60자 문자열로 일관된 출력
- 의도적 저속: 브루트포스 공격 방지를 위한 느린 처리 속도
- 블로피시 기반: Blowfish 암호화 알고리즘 기반 설계

---

## **scrypt**

- 목적: 메모리 하드 패스워드 해싱 알고리즘으로, 메모리 집약적 설계
- 속도: 의도적으로 느리며, 특히 메모리 사용량이 많음 (하드웨어 가속 공격에 대한 저항성)
- 보안: 매우 높은 보안성 (ASIC 및 GPU 기반 공격에 강한 저항성)
- 워크팩터: 다중 매개변수 조절 가능 (CPU 비용, 메모리 비용, 병렬화 인자)
- 솔팅: 기본적으로 포함 (자동 솔팅 적용)
- 출력 길이: 가변적 (설정에 따라 조정 가능)
- 특징: 대량의 메모리를 필요로 하여 특수 하드웨어를 이용한 공격 비용을 크게 증가시킴

## **scrypt 알고리즘의 특징**

- 메모리 하드: 대량의 메모리 사용을 요구하는 설계
- 다중 매개변수: CPU 비용, 메모리 비용, 병렬화 인자 조절 가능
- 하드웨어 저항성: ASIC 및 GPU 기반 공격에 강한 방어력
- 가변 출력 길이: 설정에 따라 출력 길이 조정 가능
- 자동 솔팅: 내장된 솔트 생성 및 적용 기능
- 키 유도 기능: 패스워드 해싱 외에도 키 유도 함수로 활용 가능

---

## 주요 차이점 (SHA vs bcrypt vs scrypt)

1. 용도의 차이
   - SHA: 데이터 무결성 검증, 디지털 서명, 블록체인 등 다양한 용도
   - bcrypt: 오로지 패스워드 해시에 특화됨
   - scrypt: 패스워드 해시 및 키 유도 함수로 특화, 메모리 하드웨어 공격 방어에 중점
2. 속도와 보안의 교환
   - SHA: 빠른 계산 속도 중시, 브루트포스 공격에 취약
   - bcrypt: 의도적으로 느린 계산 속도로 보안 강화
   - scrypt: 메모리 사용량과 계산 속도 모두 조절하여 하드웨어 가속 공격에 강한 저항성 제공
3. 솔팅 및 워크팩터
   - SHA: 솔팅 기능 없음, 워크팩터 조절 불가
   - bcrypt: 내장 솔팅, 단일 워크팩터(비용 인자) 조절 가능
   - scrypt: 내장 솔팅, 다중 워크팩터(CPU 비용, 메모리 비용, 병렬화 인자) 조절 가능
4. 리소스 사용
   - SHA: 최소한의 메모리와 CPU 사용
   - bcrypt: 중간 수준의 메모리와 높은 CPU 사용
   - scrypt: 높은 메모리 사용과 높은 CPU 사용 (의도적으로 메모리 집약적)
5. 출력 특성
   - SHA: 알고리즘에 따라 고정 길이 출력 (SHA-256은 256비트)
   - bcrypt: 60자 고정 길이 출력
   - scrypt: 가변 길이 출력 (설정에 따라 조정 가능)

## 사용 예시 코드 (Python)

SHA-256 예시:

```python
import hashlib

password = "mypassword"
hash_object = hashlib.sha256(password.encode())
hex_dig = hash_object.hexdigest()
print(hex_dig)
```

bcrypt 예시:

```python
import bcrypt

password = "mypassword".encode()

# 해시 생성 (솔트 자동 생성)
hashed = bcrypt.hashpw(password, bcrypt.gensalt(rounds=12))
print(hashed.decode())

# 비밀번호 검증
is_valid = bcrypt.checkpw(password, hashed)
print(is_valid)  # True
```

## 사용 가이드라인

- 데이터 무결성 검증, 디지털 서명 등에는 SHA 알고리즘 사용
- 패스워드 저장에는 bcrypt, Argon2, PBKDF2와 같은 전용 해시 함수 사용
- 절대 패스워드 저장에 순수 SHA 알고리즘만 사용하지 않음
