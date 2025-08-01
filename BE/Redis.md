
# Redis 실무 가이드 (백엔드 개발자용)

## ✅ 1. 기본 개념

| 개념 | 설명 |
|------|------|
| Redis 구조 | In-memory key-value store (빠름, 비휘발성 아님) |
| 자료형 | String, Hash, List, Set, Sorted Set, Bitmap, HyperLogLog 등 |
| TTL | key에 유효기간 설정 (`EXPIRE`) |
| 영속화 | RDB, AOF (재시작 시 데이터 유지) |
| Pub/Sub | Redis로 간단한 이벤트 메시징 가능 |

---

## ✅ 2. 주요 명령어

| 기능 | 명령어 예시 |
|------|------------|
| 값 저장 | `SET key value`, `INCR key` |
| 값 조회 | `GET key` |
| TTL 확인 | `TTL key`, `EXPIRE key 60` |
| 삭제 | `DEL key`, `FLUSHALL` |
| 키 패턴 조회 | `KEYS *`, `SCAN` (대용량에서 안전) |

---

## ✅ 3. 실무에서 자주 쓰는 기능

| 기능 | 사용 예 |
|------|---------|
| 캐싱 | `@Cacheable`, 직접 RedisTemplate 사용 |
| 분산락 | `SETNX + EXPIRE` 조합 또는 Redisson |
| 세션 저장소 | Spring Session Redis |
| 속도 제한 | Redis + AOP 조합으로 Rate Limiting |
| 큐(비동기) | `List`, `Stream`, `Pub/Sub`, `Kafka 대용` |
| 중복 체크 | Set, Bloom Filter 활용 |

---

## ✅ 4. 성능 & 운영 관련

| 항목 | 설명 |
|------|------|
| 메모리 관리 | maxmemory 정책 (volatile-lru, allkeys-lru 등) |
| eviction 정책 | 메모리가 부족할 때 어떤 key를 지울지 설정 |
| Redis Cluster | 고가용성 / 샤딩 |
| Sentinel | 자동 failover + HA 구성 |
| Redis 모니터링 | `MONITOR`, `INFO`, `Slowlog`, Grafana 등 |
| persistence | RDB, AOF 설정 비교 필요 |

---

## ✅ 5. Spring + Redis 연동

| 항목 | 내용 |
|------|------|
| RedisTemplate | 가장 많이 쓰는 방식 |
| StringRedisTemplate | value를 String으로 처리 |
| Lettuce vs Jedis | Lettuce(기본값, 비동기 가능), Jedis(동기) |
| 설정 | `application.yml`, 또는 별도 `RedisConfig` |
| 캐시 설정 | `@EnableCaching`, `@Cacheable`, `@CacheEvict` |

---

## 🔥 추천 학습 순서 (실무용 기준)

1. Redis 자료구조 (String, Hash, List, Set, Sorted Set)
2. TTL & 기본 명령어
3. 캐시 (Spring @Cacheable + RedisTemplate)
4. Rate Limiting 구현
5. 분산 락 (Redisson)
6. Redis persistence (RDB, AOF)
7. Redis 운영 (eviction, memory, cluster/sentinel)
