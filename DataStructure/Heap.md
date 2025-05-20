# 힙(Heap)과 우선순위 큐(Priority Queue)

## 힙(Heap)

힙은 완전 이진 트리 기반의 자료구조로, 특정한 규칙을 만족합니다.

### 힙의 특징

- 완전 이진 트리(Complete Binary Tree) 구조를 가집니다.
- 모든 노드는 값을 가지며, 특정 순서를 유지합니다.
- 일반적으로 배열로 구현됩니다.
- 인덱스 계산:
  - 부모 노드: `(i-1) / 2`
  - 왼쪽 자식 노드: `2*i + 1`
  - 오른쪽 자식 노드: `2*i + 2`

### 힙의 종류

1. **최대 힙(Max Heap)**
   - 부모 노드의 값이 자식 노드의 값보다 크거나 같습니다.
   - 루트 노드는 트리에서 가장 큰 값을 가집니다.

2. **최소 힙(Min Heap)**
   - 부모 노드의 값이 자식 노드의 값보다 작거나 같습니다.
   - 루트 노드는 트리에서 가장 작은 값을 가집니다.

### 힙의 주요 연산

1. **삽입(Insertion) - O(log n)**
   - 새 노드를 트리의 마지막 위치에 추가합니다.
   - 힙 속성이 유지될 때까지 부모 노드와 비교하며 위로 이동시킵니다(업힙/heapify-up).

2. **삭제(Deletion) - O(log n)**
   - 루트 노드(최대값 또는 최소값)를 제거합니다.
   - 마지막 노드를 루트로 이동시킵니다.
   - 힙 속성이 유지될 때까지 자식 노드와 비교하며 아래로 이동시킵니다(다운힙/heapify-down).

### 힙의 활용

- 힙 정렬(Heap Sort)
- 우선순위 큐 구현
- 다익스트라 알고리즘 등 그래프 알고리즘
- 중앙값 찾기(Median Finding)

## 우선순위 큐(Priority Queue)

우선순위 큐는 각 요소가 우선순위를 가지며, 우선순위가 높은 요소가 먼저 처리되는 자료구조입니다.

### 우선순위 큐의 특징

- 삽입된 순서와 상관없이 우선순위가 높은 데이터가 먼저 나옵니다.
- 일반적으로 힙으로 구현됩니다(가장 효율적).
- 최대 우선순위 큐와 최소 우선순위 큐가 있습니다.

### 우선순위 큐의 주요 연산

1. **삽입(Insertion) - O(log n)**
   - 새 요소를 우선순위 큐에 추가합니다.

2. **삭제(Deletion) - O(log n)**
   - 가장 높은 우선순위의 요소를 제거하고 반환합니다.

3. **피크(Peek) - O(1)**
   - 가장 높은 우선순위의 요소를 제거하지 않고 조회합니다.

### 우선순위 큐의 구현 방법

1. **배열/리스트 기반**
   - 삽입: O(1)
   - 삭제: O(n) (최대/최소 요소를 찾아야 함)

2. **정렬된 배열/리스트 기반**
   - 삽입: O(n) (정렬 상태 유지)
   - 삭제: O(1) (맨 앞/뒤 요소 제거)

3. **힙(Heap) 기반**
   - 삽입: O(log n)
   - 삭제: O(log n)
   - 가장 효율적인 구현 방법

4. **이진 탐색 트리 기반**
   - 삽입: O(log n)
   - 삭제: O(log n)
   - 균형이 유지되지 않으면 성능 저하

### 우선순위 큐의 활용

- 작업 스케줄링(CPU 스케줄링)
- 네트워크 트래픽 제어
- 다익스트라 알고리즘
- 허프만 코딩
- 이벤트 기반 시뮬레이션
- 데이터 압축

## 힙과 우선순위 큐의 관계

- 힙은 우선순위 큐를 구현하는 가장 효율적인 자료구조입니다.
- 최대 힙은 최대 우선순위 큐를, 최소 힙은 최소 우선순위 큐를 구현하는 데 사용됩니다.
- 힙은 완전 이진 트리라는 구체적인 구조를 가지지만, 우선순위 큐는 개념적인 자료구조입니다.

## 구현 예시 (의사 코드)

### 최소 힙 구현

```
class MinHeap:
    constructor():
        heap = []
    
    getParentIndex(i):
        return (i - 1) // 2
    
    getLeftChildIndex(i):
        return 2 * i + 1
    
    getRightChildIndex(i):
        return 2 * i + 2
    
    swap(i, j):
        heap[i], heap[j] = heap[j], heap[i]
    
    heapifyUp(index):
        parent = getParentIndex(index)
        if index > 0 and heap[parent] > heap[index]:
            swap(parent, index)
            heapifyUp(parent)
    
    heapifyDown(index):
        smallest = index
        left = getLeftChildIndex(index)
        right = getRightChildIndex(index)
        
        if left < heap.length and heap[left] < heap[smallest]:
            smallest = left
        
        if right < heap.length and heap[right] < heap[smallest]:
            smallest = right
        
        if smallest != index:
            swap(index, smallest)
            heapifyDown(smallest)
    
    insert(value):
        heap.push(value)
        heapifyUp(heap.length - 1)
    
    extractMin():
        if heap.length == 0:
            return null
        
        min = heap[0]
        lastItem = heap.pop()
        
        if heap.length > 0:
            heap[0] = lastItem
            heapifyDown(0)
        
        return min
    
    peek():
        if heap.length == 0:
            return null
        return heap[0]
```
