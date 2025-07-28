## 그래프
![graph](https://raw.githubusercontent.com/totohoon02/totohoon02.github.io/main/_images/weighted_graph.png)

#### 거리 테이블

| 1 | 0 |
| --- | --- |
| 2 | 999 |
| 3 | 999 |
| 4 | 999 |
| 5 | 999 |
| 6 | 999 |

#### 상태

-   현재 위치: 1
-   방문 정점: \[1\]

현재 정점에서 방문 가능한 노드(2)의 가중치 합 계산

1 > 2 / 2

거리 테이블과 비교 후 최소 값으로 갱신

| 1 | 0 |
| --- | --- |
| 2 | **2** |
| 3 | 999 |
| 4 | 999 |
| 5 | 999 |
| 6 | 999 |

2 를 방문

#### 상태

-   현재 위치: 2
-   방문 정점: \[1, 2\]

현재 정점에서 방문 가능한 노드(4, 5)

1 > 2 > 3 / 12

1 > 2 > 4 / 4

1 > 2 > 5 / 7

| 1 | 0 |
| --- | --- |
| 2 | 2 |
| 3 | **12** |
| 4 | **4** |
| 5 | **7** |
| 6 | 999 |

최단 거리인 4 방문

#### 상태

-   현재 위치: 4
-   방문 정점: \[1, 2, 4\]

현재 정점에서 방문 가능한 노드(5)

1 > 2 > 4 > 5 / 5

최단 거리가 5이므로 테이블 갱신

| 1 | 0 |
| --- | --- |
| 2 | 2 |
| 3 | 12 |
| 4 | 4 |
| 5 | **5** |
| 6 | 999 |

최단 거리인 5 방문

#### 상태

-   현재 위치: 5
-   방문 정점: \[1, 2, 4, 5\]

현재 정점에서 방문 가능한 노드(3, 6)

1 > 2 > 4 > 5 > 3 / 7

1 > 2 > 4 > 5 > 6 / 8

| 1 | 0 |
| --- | --- |
| 2 | 2 |
| 3 | **7** |
| 4 | 4 |
| 5 | 5 |
| 6 | **8** |

최단 거리인 3 방문

#### 상태

-   현재 위치: 3
-   방문 정점: \[1, 2, 4, 5, 3\]

현재 정점에서 방문할 정점이 없으므로 이전 노드인 5로 이동

#### 상태

-   현재 위치: 5
-   방문 정점: \[1, 2, 4, 5, 3\]

현재 정점에서 방문 가능한 노드(6) 방문

#### 상태

-   현재 위치: 6
-   방문 정점: \[1, 2, 4, 5, 3, 6\]

더 이상 방문할 정점이 없으므로 탐색 종료

#### 시작 정점에서 각 지점에 이르는 최단 거리

| 1 | 0 |
| --- | --- |
| 2 | 2 |
| 3 | 7 |
| 4 | 4 |
| 5 | 5 |
| 6 | 8 |

#### 예시

```python
import heapq
from collections import defaultdict

# 그래프 정의
graph = defaultdict(list)
edges = [
    (1, 2, 2),
    (2, 3, 10),
    (2, 4, 2),
    (2, 5, 5),
    (4, 5, 1),
    (5, 3, 2),
    (5, 6, 3),
    (6, 3, 2)
]

for u, v, w in edges:
    graph[u].append((v, w))

def dijkstra(start):
    distance = defaultdict(lambda: float('inf'))
    visited = set()
    distance[start] = 0
    pq = [(0, start)]

    while pq:
        dist, node = heapq.heappop(pq)

        if node in visited:
            continue
        visited.add(node)

        for neighbor, weight in graph[node]:
            new_dist = dist + weight
            if new_dist < distance[neighbor]:
                distance[neighbor] = new_dist
                heapq.heappush(pq, (new_dist, neighbor))

    return dict(distance)
```
