# AWS 로드 밸런서 ELB (ALB, NLB, CLB, GWLB) 특징 및 차이점 정리

## ELB (Elastic Load Balancing) 개요

AWS의 ELB는 들어오는 트래픽을 여러 가용 영역(AZ)의 애플리케이션에 자동으로 분산하여 고가용성과 확장성을 제공하는 서비스입니다. ELB는 EC2, ECS, Lambda 등 다양한 AWS 서비스와 연계하여 트래픽 부하를 분배할 수 있습니다.

---

## ELB의 4가지 종류

### 1. Classic Load Balancer (CLB)

- **지원 프로토콜**: HTTP, HTTPS, TCP, SSL/TLS
- **레이어**: Layer 4 (Transport Layer) 및 Layer 7 (Application Layer)
- **특징**: 기본적인 로드 밸런싱 기능 제공, 고급 기능 부족
- **사용 권장 여부**: 신규 서비스에는 사용 비추천, 기존 사용자에게는 계속 지원 중

### 2. Application Load Balancer (ALB)

- **지원 프로토콜**: HTTP, HTTPS
- **레이어**: Layer 7 (Application Layer)
- **특징**:
  - 고급 라우팅 기능: Path-Based Routing, Host-Based Routing, URL 쿼리 문자열 기반 라우팅
  - Docker 컨테이너화된 애플리케이션과의 통합 용이
  - Sticky Session 지원
  - 고정 IP 주소 사용 불가 (NLB를 앞에 놓아 고정 IP 주소 사용 가능)
  - 리스너 규칙을 통한 세밀한 트래픽 라우팅 가능

### 3. Network Load Balancer (NLB)

- **지원 프로토콜**: TCP, UDP, TLS
- **레이어**: Layer 4 (Transport Layer)
- **특징**:
  - 고성능, 낮은 지연 시간으로 대량 트래픽 처리 가능
  - 고정 IP 주소 지원
  - Lambda와의 통합 불가 (ALB는 지원)
  - Sticky Session 사용 불가

### 4. Gateway Load Balancer (GWLB)

- **지원 프로토콜**: TCP/UDP
- **레이어**: Layer 4 (Transport Layer)
- **특징**:
  - 방화벽, 침입 탐지 및 방지 시스템(IDS/IPS), 심층 패킷 검사 시스템과 같은 가상 어플라이언스를 배포, 확장 및 관리 가능
  - 트래픽이 도달하기 전에 먼저 트래픽을 검사하거나 분석하는 작업을 수행할 수 있게 하는 서비스

---

## ELB의 주요 기능

- **Health Checks**: 애플리케이션 서버의 상태를 설정된 시간 간격으로 확인하고, 문제가 있을 경우 트래픽을 해당 서버로 전송하지 않음
- **Slow Start Configuration**: ALB와 NLB에서 지원하며, 새로 시작된 인스턴스를 위해 천천히 점진적으로 트래픽을 부하하는 기능
- **Connection Draining**: CLB에서 지원하며, 인스턴스가 등록 해제되거나 Unhealthy 되었을 때 지정된 시간만큼 연결을 기다려주는 기능

---

## 사용 사례 비교

| 로드 밸런서 | 주로 사용되는 사례                                              |
| ----------- | --------------------------------------------------------------- |
| ALB         | 웹 애플리케이션, 고급 라우팅이 필요한 경우                      |
| NLB         | 고속 트래픽이 요구되는 애플리케이션, 고정 IP 주소가 필요한 경우 |
| CLB         | 오래된 legacy 애플리케이션                                      |
| GWLB        | 트래픽 검사 및 분석이 필요한 경우, 보안 솔루션과의 통합         |

---

## 참고 자료

- 원문: [https://jibinary.tistory.com/196](https://jibinary.tistory.com/196)
