# Network

## 프로토콜

- 규약
- 데이터를 어떻게 전송할지 정의

## TCP/IP

- TCP
  - Transmission Control Protocol
  - 연결 지향적
  - 데이터를 어떻게 전송할지 정의
- IP
  - Internet Protocol
  - 연결 비지향적
  - 데이터를 어떻게 전송할지 정의

### TCP/IP 5 계층 모델 정리

#### 1. 물리 계층 (Physical)

- **역할**
  - 0·1 비트를 전기/광 신호로 변환·전송·수신
- **주요 요소**
  - 케이블(UTP, 광), 커넥터, 리피터, 허브, 전송 부호(NRZ, 8b/10b)
- **핵심 포인트**
  - 인코딩/디코딩
  - 감쇠·노이즈 관리
  - 대역폭·지연
  - 임피던스 매칭

#### 2. 데이터링크 계층 (Data Link)

- **역할**
  - 프레임 단위 전송
  - MAC 주소 기반 식별
  - 오류 검출/재전송
- **대표 프로토콜**
  - Ethernet(802.3), Wi-Fi(802.11), PPP, HDLC, **ARP**
- **핵심 포인트**
  - 프레이밍(Preamble, MAC Header, FCS)
  - CSMA/CD·CA
  - 스위치 MAC 테이블
  - STP/RSTP

#### 3. 네트워크 계층 (Network)

- **역할**
  - 패킷 라우팅
  - 논리 주소(IP) 관리
- **대표 프로토콜**
  - IPv4/IPv6, ICMP, OSPF, BGP, IPsec
- **핵심 포인트**
  - 라우팅(IGP/EGP)
  - 서브넷팅·CIDR
  - NAT
  - 오버레이(VXLAN)
  - Dual-Stack IPv6

#### 4. 트랜스포트 계층 (Transport)

- **역할**
  - 세그먼트/데이터그램 전송의 신뢰성·혼잡·순서 제어
- **대표 프로토콜**
  - TCP, UDP, QUIC, SCTP
- **핵심 포인트**
  - TCP 3-way handshake
  - 흐름·혼잡 제어(CUBIC/BBR)
  - 포트 번호
  - L4 로드밸런싱

#### 5. 애플리케이션 계층 (Application)

- **역할**
  - 사용자·프로세스가 직접 사용하는 서비스 프로토콜 제공
- **대표 프로토콜**
  - HTTP(S), FTP(S), SMTP/IMAP, DNS, SSH, MQTT, gRPC
- **핵심 포인트**
  - HTTP/1.1 → 2 → 3(QUIC)
  - TLS 종료
  - OAuth/OIDC
  - RPC(gRPC, GraphQL)
  - IoT용 MQTT/CoAP

### 계층 간 캡슐화 흐름

```text
Application
        ↓ +헤더 / 데이터그램
Transport
        ↓ +헤더 / 세그먼트
Network
        ↓ +헤더 / 패킷
Data Link
        ↓ +트레일러 +헤더 / 프레임
Physical (신호)
```
