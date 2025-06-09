# MLFlow

### MLOps

- 머신러닝 + 데브옵스
- 머신러닝 워크플로의 자동화, 실행

### Key Components of MLOps

- Model Deployment
  - .pkl, .onnx
  - Deploy Model(Rest API, Docker, FastAPI)
- Data Management
  - Dataset handling
  - Airflow, Kafka
- Model Monitoring
  - Prometheus, Grafana
- Model Development
  - 모델 버저닝
  - 하이퍼파리미터 튜닝
- CI/CD
  - Github Actions
- Governance and Compliance

### MLOps Workflow

- Data Preperation
- Model Development
- Model Training
- Model Validation
- Model Deployment
- Monitoring

### Machine Learning Lifecycle(Traditional)

- Problem Definition

  - 문제 정의
  - 목표 설정

- Data Collection

  - 데이터 수집
  - 데이터 저장

- Data Preprocessing

  - 데이터 전처리
  - 데이터 정제
  - 데이터 셋 구성
  - 데이터 엔지니어링

- Exploratory Data Analysis(EDA)

  - 데이터 탐색
  - 데이터 분석
  - 데이터 시각화
  - 데이터 사이언티스트

- Model Selection

  - 알고리즘 선택
  - 하이퍼파라미터 튜닝

- Model Training

- Model Evaluation

- Model Deployment

- Model Monitoring
  - 모델 성능 모니터링
- Model Maintenance and Iteration
  - 모델 유지보수
  - 모델 업데이트

#### Challenges of Traditional ML Lifecycle

- 버전 컨트롤의 부재
- 팀 간의 소통 어려움
- 매뉴얼한 라이프 사이클
- 스케일 이슈(샘플 -> 실제 데이터 괴리)

### MLFlow

- 전체 머신러닝 라이프사이클 관리
  - 실험 관리
  - 모델 버저닝
  - 모델 추적
  - 모델 배포
  - 모델 모니터링
  - 모델 유지보수
  - 모델 업데이트
- 모델 트래킹
- 레지스트리

### MLFlow Install

#### 깃허브 토큰 생성

- Settings -> Developer Settings -> Personal Access Tokens -> Generate new token
- 이미지를 받으려면 토큰 생성 후 로그인 필요

```bash
export CR_PAT="Your token..."

export CR_PAT=$CR_PAT
echo $CR_PAT | docker login ghcr.io -u totohoon02 --password-stdin

docker pull ghcr.io/mlflow/mlflow:v2.20.2
```

#### 실행

- 컨테이너에 접속

```bash
python tutorial.py # mlflow WebUI에서 실행 결과 확인
```
