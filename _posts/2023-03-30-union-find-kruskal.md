---
title: "[코테] 서로소 집합(Union-Find)과 크루스칼 - 최소 신장 트리"
date: 2023-03-30 22:10:00 +0900
categories: [Algorithm, 그래프]
tags: [python, baekjoon, 유니온파인드, 크루스칼, mst]
---

## 간단한 소개

**서로소 집합(Union-Find)** 은 "두 노드가 같은 그룹인지"를 빠르게 판단하는 자료구조.
이걸 이용해서 모든 노드를 **최소 비용으로 연결**하는 게 크루스칼 알고리즘이다.
두 개가 붙어서 나오는 경우가 많아서 같이 정리.

## 개념

### 서로소 집합

- 각 노드가 자기 **부모(대표)** 를 가리킨다
- `find(x)` : x가 속한 집합의 대표(루트) 찾기
- `union(a, b)` : a와 b의 집합을 하나로 합치기
- 대표가 같으면 같은 집합

### 최소 신장 트리 (MST)

- **신장 트리** : 모든 노드를 포함하면서 사이클이 없는 트리 (간선 N-1개)
- **최소 신장 트리** : 그중 간선 비용 합이 최소인 것
- 예 : 도시 전부를 최소 비용 도로로 연결하기

## 원리

### Union-Find

```text
처음 : 1  2  3  4      (각자 자기 자신이 대표)

union(1, 2) :  1       union(3, 4) :  1     3
               |                      |     |
               2                      2     4

union(2, 4) :      1
                 / \
                2   3
                    |
                    4        find(4) = 1
```

- **경로 압축** : find 하면서 지나간 노드들이 바로 루트를 가리키게 바꿔줌 → 거의 O(1)

### 크루스칼

![크루스칼](/assets/img/posts/algorithm/kruskal.png){: width="560" }
_크루스칼로 만든 최소 신장 트리_

1. 간선을 **비용 오름차순 정렬**
2. 싼 간선부터 보면서, 두 노드가 **다른 집합이면** 연결 (union)
3. 같은 집합이면 연결 시 **사이클이 생기니까** 건너뜀
4. 간선 N-1개를 고르면 끝

정렬 + 그리디 + 유니온파인드 조합이다.

## 코드

### Union-Find 템플릿

```python
import sys
sys.setrecursionlimit(10 ** 6)

parent = list(range(n + 1))

def find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])   # 경로 압축
    return parent[x]

def union(a, b):
    a, b = find(a), find(b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b
```

### 1717 집합의 표현

```python
import sys
sys.setrecursionlimit(10 ** 6)
input = sys.stdin.readline

n, m = map(int, input().split())
parent = list(range(n + 1))

def find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])
    return parent[x]

out = []
for _ in range(m):
    op, a, b = map(int, input().split())
    if op == 0:
        ra, rb = find(a), find(b)
        if ra != rb:
            parent[rb] = ra
    else:
        out.append('YES' if find(a) == find(b) else 'NO')
print('\n'.join(out))
```

### 1197 최소 스패닝 트리 (크루스칼)

```python
import sys
input = sys.stdin.readline

V, E = map(int, input().split())
edges = [tuple(map(int, input().split())) for _ in range(E)]
edges.sort(key=lambda e: e[2])        # 비용 순 정렬

parent = list(range(V + 1))
def find(x):
    while parent[x] != x:             # 재귀 대신 반복문 (깊이 걱정 X)
        parent[x] = parent[parent[x]]
        x = parent[x]
    return x

total = 0
cnt = 0
for a, b, c in edges:
    ra, rb = find(a), find(b)
    if ra != rb:                      # 다른 집합일 때만 연결
        parent[rb] = ra
        total += c
        cnt += 1
        if cnt == V - 1:
            break
print(total)
```

### 프림 (참고)

크루스칼은 간선 중심, 프림은 **노드 중심**. 한 노드에서 시작해서 연결된 간선 중 제일 싼 걸 힙으로 계속 고른다.

```python
import heapq

visited = [False] * (V + 1)
h = [(0, 1)]
total = 0
while h:
    w, v = heapq.heappop(h)
    if visited[v]:
        continue
    visited[v] = True
    total += w
    for nxt, c in graph[v]:
        if not visited[nxt]:
            heapq.heappush(h, (c, nxt))
```

## 시간복잡도 / 효율성

| | 시간복잡도 |
| --- | --- |
| find / union (경로 압축) | 거의 O(1) |
| 크루스칼 | **O(E log E)** (정렬이 대부분) |
| 프림 (힙) | O(E log V) |

- 간선이 적으면 크루스칼, 간선이 아주 많으면 프림이 유리
- 코테에서는 크루스칼이 구현하기 편해서 주로 이걸 씀

> Union-Find는 **무방향 그래프의 사이클 판별**에도 쓴다. union 하려는데 이미 같은 집합이면 사이클.
{: .prompt-tip }

## 관련 문제
- 1717 집합의 표현
- 1976 여행 가자
- 1197 최소 스패닝 트리
- 1922 네트워크 연결
- 1647 도시 분할 계획