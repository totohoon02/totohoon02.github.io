# Network Address Port Translation

- 공인 IP <=> 사설 IP의 포트 기반 변환
- 사설(N) : 공인(1)의 대응
- 외부에서 사설망의 실제 IP를 알 수 없어 보안성 향상 효과

| Private IP   | Private Port | Public IP | Public Port |
|--------------|--------------|-----------|-------------|
| 192.168.10.2 | 8000         | 1.2.3.4   | 8001        |
| 192.168.10.3 | 8000         | 1.2.3.4   | 8002        |

### NAT 종류

- Static NAT
  - 내/외부 1:1 매핑
- Dynamic NAT
  - NAT 풀에 있는 공인 IP를 동적으로 할당
- PAT/NAPT
  - 하나의 공인 IP를 공유, 포트 번호로 매핑
