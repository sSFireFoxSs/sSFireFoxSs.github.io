---
title: "[코테] 트리 순회 - 전위, 중위, 후위, 레벨"
date: 2023-02-15 00:35:00 +0900
categories: [Algorithm, 그래프]
tags: [python, baekjoon, 트리, 순회]
---

## 간단한 소개

트리의 모든 노드를 정해진 순서로 한 번씩 방문하는 방법.
자료구조 시험에도 나오고, 1991번이 딱 이거라서 정리.

## 개념

**루트(자기 자신)를 언제 방문하냐**로 나뉜다.

| 순회 | 순서 | 외우는 법 |
| --- | --- | --- |
| 전위 (Preorder) | 루트 → 왼쪽 → 오른쪽 | 루트가 **앞** |
| 중위 (Inorder) | 왼쪽 → 루트 → 오른쪽 | 루트가 **가운데** |
| 후위 (Postorder) | 왼쪽 → 오른쪽 → 루트 | 루트가 **뒤** |
| 레벨 (Level order) | 위층부터 왼쪽 → 오른쪽 | BFS랑 똑같음 |

## 원리

```text
          A
        /   \
       B     C
      / \     \
     D   E     F

전위 : A B D E C F
중위 : D B E A C F
후위 : D E B F C A
레벨 : A B C D E F
```

- 전위/중위/후위는 **DFS(재귀)** 로 구현
- 레벨 순회는 **BFS(큐)** 로 구현
- 이진 탐색 트리를 중위 순회하면 **정렬된 순서**가 나온다

## 코드

### 1991 트리 순회

```python
n = int(input())
tree = {}
for _ in range(n):
    p, l, r = input().split()
    tree[p] = (l, r)

def pre(v):
    if v == '.':
        return ''
    l, r = tree[v]
    return v + pre(l) + pre(r)

def ino(v):
    if v == '.':
        return ''
    l, r = tree[v]
    return ino(l) + v + ino(r)

def post(v):
    if v == '.':
        return ''
    l, r = tree[v]
    return post(l) + post(r) + v

print(pre('A'))
print(ino('A'))
print(post('A'))
```

세 함수가 `v`를 붙이는 위치만 다르다. 이렇게 보니까 바로 외워졌다.

### 레벨 순회

```python
from collections import deque

def level(root):
    q = deque([root])
    res = []
    while q:
        v = q.popleft()
        if v == '.':
            continue
        res.append(v)
        l, r = tree[v]
        q.append(l)
        q.append(r)
    return ''.join(res)
```

### 11725 트리의 부모 찾기

일반 트리(이진 아님)는 루트부터 BFS 돌면서 부모를 기록하면 된다.

```python
import sys
from collections import deque
input = sys.stdin.readline

n = int(input())
graph = [[] for _ in range(n + 1)]
for _ in range(n - 1):
    a, b = map(int, input().split())
    graph[a].append(b)
    graph[b].append(a)

parent = [0] * (n + 1)
parent[1] = -1
q = deque([1])
while q:
    v = q.popleft()
    for nxt in graph[v]:
        if parent[nxt] == 0:
            parent[nxt] = v
            q.append(nxt)

print('\n'.join(map(str, parent[2:])))
```

## 시간복잡도 / 효율성

- 모든 노드를 한 번씩 방문 → **O(N)**
- 재귀 순회는 트리가 한쪽으로 쭉 늘어지면 깊이가 N까지 가니까 `setrecursionlimit` 필요

## 관련 문제
- 1991 트리 순회
- 11725 트리의 부모 찾기
- 5639 이진 검색 트리
- 2263 트리의 순회 (어려움)