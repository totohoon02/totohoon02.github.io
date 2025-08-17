# Airflow

### Datapipeline

- 데이터의 ETL 과정
  - Extract
  - Transform
  - Load
- 데이터의 종류는 매우 다양하다.
- Batch, Realtime
- Batch Service
  - 일정 시간마다 데이터를 처리해서 적재
- Realtime Service
  - 데이터가 발생할 때마다 즉시 처리
- Datapipeline Orchestration

### Orchestration

- 데이터 파이프라인 관리
  - 워크플로 정의
  - 스케줄링
  - 의존성 관리(A -> B -> C)
  - 데이터 이동
  - 모니터링, 에러 핸들링
  - Scale, Parallel
- 데이터 파이프라인이 부드럽게 돌아갈 수 있도록 관리

### Airflow

- Python 코드 기반 워크플로 관리
- Key Components
  - Webserver
  - Scheduler
    - DAG 관리
  - Metadata Database
    - DAG 메타데이터 저장
  - Executor
  - Worker
  - Trigger
    - 비동기 Worker
    - 대기 시간 동안 리소스 점유 없음

### DAG(Directed Acyclic Graph)

- 워크플로를 정의하는 단방향 그래프
- 각 작업을 실행하는 task의 모음

### Airflow Architecture

- Single node
  - Key Components가 한 노드에 설치
- Multi node
  - Key Components가 여러 노드에 설치
  - Webserver, Scheduler, Executor
  - Metadata Database
  - Queue
  - Worker

### 설치하기

- standalone
- docker

```bash
# https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html

# download docker-compose.yaml
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/3.0.1/docker-compose.yaml'

# 해당 폴더로 이동
echo AIRFLOW_UID=50000 > .env

# 실행
docker compose up
```

- 기본 id, pw
  - airflow / airflow
