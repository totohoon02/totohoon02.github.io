# TCP/IP와 UDP 프로토콜

## TCP/IP (Transmission Control Protocol/Internet Protocol)

TCP/IP는 인터넷에서 컴퓨터들이 서로 통신하기 위한 표준 프로토콜 집합입니다. 이 프로토콜 스택은 네트워크 통신의 기초를 형성하며, 데이터가 어떻게 패킷으로 나뉘고, 주소가 지정되고, 전송되고, 라우팅되고, 수신되는지를 정의합니다.

### TCP의 주요 특징

1. **연결 지향적(Connection-oriented)**
   - 통신 전에 3-way 핸드셰이크를 통해 연결을 설정
   - 데이터 전송 후 4-way 핸드셰이크로 연결 종료

2. **신뢰성 있는 전송(Reliable Transmission)**
   - 패킷 손실 시 재전송 메커니즘을 통해 데이터 무결성 보장
   - 확인 응답(ACK) 시스템을 통한 데이터 수신 확인

3. **순서 보장(Ordered Delivery)**
   - 데이터가 전송된 순서대로 수신자에게 전달
   - 시퀀스 번호를 사용하여 패킷 순서 유지

4. **흐름 제어(Flow Control)**
   - 수신자의 처리 능력에 맞춰 송신 속도 조절
   - 슬라이딩 윈도우 메커니즘을 통한 효율적인 데이터 전송

5. **혼잡 제어(Congestion Control)**
   - 네트워크 혼잡 상태를 감지하고 전송 속도 조절
   - 슬로우 스타트, 혼잡 회피, 빠른 재전송 등의 알고리즘 사용

### TCP 세그먼트 구조

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |           |U|A|P|R|S|F|                               |
| Offset| Reserved  |R|C|S|S|Y|I|            Window             |
|       |           |G|K|H|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options                    |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### TCP 연결 설정 과정 (3-way Handshake)

1. **SYN**: 클라이언트가 서버에 연결 요청 (SYN 플래그 설정)
2. **SYN-ACK**: 서버가 클라이언트의 요청을 수락하고 응답 (SYN, ACK 플래그 설정)
3. **ACK**: 클라이언트가 서버의 응답을 확인 (ACK 플래그 설정)

### TCP 연결 종료 과정 (4-way Handshake)

1. **FIN**: 클라이언트가 연결 종료 요청 (FIN 플래그 설정)
2. **ACK**: 서버가 클라이언트의 요청 확인 (ACK 플래그 설정)
3. **FIN**: 서버가 연결 종료 준비 완료 알림 (FIN 플래그 설정)
4. **ACK**: 클라이언트가 서버의 종료 준비 확인 (ACK 플래그 설정)

## UDP (User Datagram Protocol)

UDP는 TCP와 달리 연결 설정 없이 데이터를 전송하는 단순한 프로토콜입니다. 신뢰성보다는 속도와 효율성에 중점을 둡니다.

### UDP의 주요 특징

1. **비연결형(Connectionless)**
   - 연결 설정 과정 없이 바로 데이터 전송
   - 핸드셰이크 과정이 없어 오버헤드 감소

2. **비신뢰성(Unreliable)**
   - 패킷 손실, 중복, 순서 변경에 대한 보장 없음
   - 데이터 전송 확인 메커니즘 없음

3. **순서 보장 없음(No Ordered Delivery)**
   - 패킷이 전송된 순서와 다르게 도착할 수 있음
   - 애플리케이션 레벨에서 순서 처리 필요

4. **낮은 오버헤드(Low Overhead)**
   - 헤더 크기가 작고(8바이트) 처리 과정이 단순
   - 빠른 데이터 전송 가능

5. **브로드캐스트 지원(Broadcast Support)**
   - 다수의 수신자에게 동시에 데이터 전송 가능
   - 멀티캐스트, 브로드캐스트에 적합

### UDP 데이터그램 구조

```
 0      7 8     15 16    23 24    31
+--------+--------+--------+--------+
|     Source      |   Destination   |
|      Port       |      Port       |
+--------+--------+--------+--------+
|                 |                 |
|     Length      |    Checksum     |
+--------+--------+--------+--------+
|                                   |
|              Data                 |
|                                   |
+-----------------------------------+
```

## TCP와 UDP 비교

| 특성 | TCP | UDP |
|------|-----|-----|
| 연결 방식 | 연결 지향적 | 비연결형 |
| 신뢰성 | 높음 (패킷 손실 복구) | 낮음 (패킷 손실 가능) |
| 순서 보장 | 보장됨 | 보장되지 않음 |
| 속도 | 상대적으로 느림 | 빠름 |
| 헤더 크기 | 20-60 바이트 | 8 바이트 |
| 흐름/혼잡 제어 | 있음 | 없음 |
| 적합한 용도 | 파일 전송, 웹 브라우징, 이메일 | 실시간 스트리밍, 게임, VoIP |

## 게임 개발에서의 TCP와 UDP 활용

### TCP 활용 사례

1. **계정 인증 및 로그인**
   - 사용자 인증 정보는 손실 없이 안전하게 전송되어야 함

2. **게임 상태 동기화**
   - 중요한 게임 상태 변경은 신뢰성 있게 전송되어야 함

3. **아이템 거래 및 구매**
   - 금전적 거래는 데이터 무결성이 중요함

4. **채팅 시스템**
   - 메시지가 순서대로 누락 없이 전달되어야 함

### UDP 활용 사례

1. **실시간 위치 업데이트**
   - 캐릭터 위치 정보는 빠른 전송이 중요하며, 일부 손실이 허용됨

2. **음성 채팅**
   - 실시간 음성 통신은 지연보다 일부 패킷 손실이 더 허용됨

3. **대규모 멀티플레이어 게임**
   - 많은 플레이어의 동시 접속 시 오버헤드 감소가 중요함

4. **게임 내 물리 시뮬레이션**
   - 빠른 업데이트가 필요하며, 일부 정확성 손실은 허용됨

## 하이브리드 접근법

많은 현대 게임은 TCP와 UDP를 함께 사용하는 하이브리드 접근법을 채택합니다:

1. **중요 데이터**: TCP를 통해 신뢰성 있게 전송
2. **실시간 업데이트**: UDP를 통해 빠르게 전송
3. **자체 신뢰성 레이어**: UDP 위에 게임에 최적화된 신뢰성 메커니즘 구현

## 구현 예시 (C++)

### TCP 소켓 프로그래밍 기본 예시

```cpp
// TCP 서버 예시 (간략화된 코드)
#include <iostream>
#include <winsock2.h>

#pragma comment(lib, "ws2_32.lib")

int main() {
    // 윈속 초기화
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2, 2), &wsaData);
    
    // 소켓 생성
    SOCKET serverSocket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    
    // 서버 주소 설정
    sockaddr_in serverAddr;
    serverAddr.sin_family = AF_INET;
    serverAddr.sin_addr.s_addr = INADDR_ANY;
    serverAddr.sin_port = htons(8888);
    
    // 바인딩
    bind(serverSocket, (sockaddr*)&serverAddr, sizeof(serverAddr));
    
    // 리스닝
    listen(serverSocket, SOMAXCONN);
    
    // 클라이언트 연결 수락
    sockaddr_in clientAddr;
    int clientAddrSize = sizeof(clientAddr);
    SOCKET clientSocket = accept(serverSocket, (sockaddr*)&clientAddr, &clientAddrSize);
    
    // 데이터 송수신
    char buffer[1024];
    recv(clientSocket, buffer, sizeof(buffer), 0);
    send(clientSocket, "Response", 8, 0);
    
    // 소켓 닫기
    closesocket(clientSocket);
    closesocket(serverSocket);
    
    // 윈속 정리
    WSACleanup();
    
    return 0;
}
```

### UDP 소켓 프로그래밍 기본 예시

```cpp
// UDP 서버 예시 (간략화된 코드)
#include <iostream>
#include <winsock2.h>

#pragma comment(lib, "ws2_32.lib")

int main() {
    // 윈속 초기화
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2, 2), &wsaData);
    
    // 소켓 생성
    SOCKET serverSocket = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);
    
    // 서버 주소 설정
    sockaddr_in serverAddr;
    serverAddr.sin_family = AF_INET;
    serverAddr.sin_addr.s_addr = INADDR_ANY;
    serverAddr.sin_port = htons(8888);
    
    // 바인딩
    bind(serverSocket, (sockaddr*)&serverAddr, sizeof(serverAddr));
    
    // 데이터 수신 및 응답
    char buffer[1024];
    sockaddr_in clientAddr;
    int clientAddrSize = sizeof(clientAddr);
    
    recvfrom(serverSocket, buffer, sizeof(buffer), 0, (sockaddr*)&clientAddr, &clientAddrSize);
    sendto(serverSocket, "Response", 8, 0, (sockaddr*)&clientAddr, clientAddrSize);
    
    // 소켓 닫기
    closesocket(serverSocket);
    
    // 윈속 정리
    WSACleanup();
    
    return 0;
}
```

## 결론

TCP와 UDP는 각각 고유한 특성과 장단점을 가지고 있으며, 게임 개발에서는 요구사항에 따라 적절한 프로토콜을 선택하거나 두 프로토콜을 함께 사용하는 것이 중요합니다. 신뢰성이 중요한 데이터는 TCP를, 실시간성이 중요한 데이터는 UDP를 사용하는 것이 일반적인 접근법입니다.
