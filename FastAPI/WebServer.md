# WebServer

## WSGI

- Web Server Gateway Interface
- 동기 인터페이스
- `Gunicorn`이 `WSGI`의 구현체

| 항목            | 내용                                                             |
| --------------- | ---------------------------------------------------------------- |
| 실행 방식       | 동기(Sync), 요청을 순차적으로 처리                               |
| 예시 프레임워크 | Flask, 기존 Django                                               |
| 서버 예시       | Gunicorn, uWSGI                                                  |
| 실시간 처리     | ❌ WebSocket 지원 안 됨                                          |
| 단점            | 비동기 처리를 하려면 별도 워커나 쓰레드 필요 → 복잡하고 비효율적 |

## ASGI

- Asynchronous Server Gateway Interface
- 비동기 인터페이스
- `Uvicorn`이 `ASGI`의 구현체

| 항목            | 내용                                         |
| --------------- | -------------------------------------------- |
| 실행 방식       | 비동기(async), 동기 모두 지원                |
| 예시 프레임워크 | FastAPI, Starlette, Django(3.0+ 일부 지원)   |
| 서버 예시       | Uvicorn, Daphne                              |
| 실시간 처리     | ✅ WebSocket, long polling, SSE 등 완전 지원 |
| 확장성          | ✅ 고성능 비동기 처리 및 병렬 요청 처리 가능 |

## 프로세스, 스레드 처리

- 파이썬은 기본적으로 단일 프로세스, 단일 스레드 환경에서 동작함
- CPython 인터프리터는 GIL(Global Interpreter Lock)이라는 특성 때문에 동시에 한 개의 스레드만 Python 바이트코드를 실행
- I/O 바운드 작업 대기 GIL은 다른 스레드에 실행권을 넘김
- CPU 바운드 작업 시에는 병렬처리 불가
  - `multiprocessing` 모듈 사용
- `PyPy` 같은 다른 인터프리터 고려
  
### 어떻게 여러 작업을 처리?

#### 비동기(asyncio), 멀티스레딩 기반 처리

```python
import asyncio

async def fetch():
    print("작업 시작")
    await asyncio.sleep(1)
    print("작업 완료")

async def main():
    await asyncio.gather(fetch(), fetch(), fetch())

asyncio.run(main())
```

```python
import threading
import time

def task():
    time.sleep(1)
    print("작업 완료")

for _ in range(5):
    threading.Thread(target=task).start()
```

- I/O작업(`HTTP`, `DB`, `파일처리`)이 많은 경우
- 이벤트 루프를 통해 단일 프로세스에서 다수 작업을 처리
  - I/O 대기 시 다른 작업으로 전환해 동시에 처리하는 것처럼 보임
  - CPU 유휴 상태에 따라서 작업을 스위칭

#### 멀티프로세싱

```bash
uvicorn app:app --workers 4
```

- GIL을 피하기 위해 아예 프로세스를 분리
- 코어 수 많큼 병렬 실행
- 도커를 사용할 경우 워커 프로세스 필요 없음

#### GIL

📌 개념

- CPython 인터프리터에서만 존재
- Python 객체에 대한 메모리 접근을 한 번에 하나의 스레드만 허용
- 멀티스레딩이라도 동시에 실행되는 건 아님

🎯 GIL의 목적

- 메모리 관리(가비지 컬렉션)를 안전하게 하기 위해 Python 내부 객체에 대한 동시 접근을 차단

⚠️ 문제점

- threading.Thread를 여러 개 사용하더라도, 한 번에 하나의 스레드만 실행됨
- CPU-bound 작업에서는 멀티스레딩 성능이 오히려 안 좋아질 수 있음
