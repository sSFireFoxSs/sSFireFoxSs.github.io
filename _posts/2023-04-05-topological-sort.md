---
title: "[코테] 위상 정렬 (Topological Sort)"
date: 2023-04-05 21:35:00 +0900
categories: [Algorithm, 그래프]
tags: [python, baekjoon, 위상정렬, 그래프]
---

## 간단한 소개

**순서가 정해진 작업들**을 순서에 맞게 나열하는 방법.
"선수과목 들어야 다음 과목 수강 가능" 같은 문제. 코테 알고리즘 정리는 여기까지 하고 마무리.

## 개념

- **방향 그래프**에서 간선 방향을 거스르지 않도록 노드를 나열
- **사이클이 없어야** 가능 (DAG, Directed Acyclic Graph)
- **진입차수(indegree)** : 나한테 들어오는 간선 개수
  - 진입차수 0 = 먼저 해야 할 게 없는 노드 = 지금 바로 할 수 있음

## 원리

```text
자료구조 ──▶ 알고리즘 ──▶ 코테
   │                      ▲
   └──▶ 파이썬 ────────────┘

진입차수 : 자료구조 0, 알고리즘 1, 파이썬 1, 코테 2
```

1. 진입차수가 0인 노드를 전부 큐에 넣는다
2. 큐에서 하나 꺼내서 결과에 추가
3. 그 노드에서 나가는 간선을 지우고 (연결된 노드의 진입차수 -1)
4. 새로 진입차수가 0이 된 노드를 큐에 넣는다
5. 큐가 빌 때까지 반복

결과 : 자료구조 → 알고리즘 → 파이썬 → 코테 (답이 여러 개일 수 있음)

> 다 끝났는데 결과에 들어간 노드가 N개보다 적으면 **사이클이 있다**는 뜻
{: .prompt-info }

## 코드

### 2252 줄 세우기

```python
import sys
from collections import deque
input = sys.stdin.readline

n, m = map(int, input().split())
graph = [[] for _ in range(n + 1)]
indegree = [0] * (n + 1)

for _ in range(m):
    a, b = map(int, input().split())     # a가 b보다 앞
    graph[a].append(b)
    indegree[b] += 1

q = deque(i for i in range(1, n + 1) if indegree[i] == 0)
result = []
while q:
    v = q.popleft()
    result.append(v)
    for nxt in graph[v]:
        indegree[nxt] -= 1
        if indegree[nxt] == 0:
            q.append(nxt)

print(*result)
```

### 1766 문제집 (번호 작은 것부터)

가능한 것 중 **번호가 작은 것부터** 풀어야 해서 큐 대신 **힙**을 쓴다.

```python
import sys, heapq
input = sys.stdin.readline

n, m = map(int, input().split())
graph = [[] for _ in range(n + 1)]
indegree = [0] * (n + 1)
for _ in range(m):
    a, b = map(int, input().split())
    graph[a].append(b)
    indegree[b] += 1

h = [i for i in range(1, n + 1) if indegree[i] == 0]
heapq.heapify(h)
result = []
while h:
    v = heapq.heappop(h)
    result.append(v)
    for nxt in graph[v]:
        indegree[nxt] -= 1
        if indegree[nxt] == 0:
            heapq.heappush(h, nxt)

print(*result)
```

## 시간복잡도 / 효율성

- 모든 노드와 간선을 한 번씩 → **O(V + E)**
- 힙 버전은 O((V + E) log V)

## 관련 문제
- 2252 줄 세우기
- 1766 문제집
- 1005 ACM Craft
- 2623 음악프로그램