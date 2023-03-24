---
title: "[코테] 플로이드 워셜 (+ 벨만-포드 조금)"
date: 2023-03-24 23:30:00 +0900
categories: [Algorithm, 그래프]
tags: [python, baekjoon, 플로이드워셜, 벨만포드, 최단경로]
---

## 간단한 소개

**모든 노드에서 모든 노드까지**의 최단 거리를 한 번에 구하는 방법.
코드가 삼중 for문 하나라 다익스트라보다 훨씬 간단하다. 대신 느려서 노드가 적을 때만 쓴다.

## 개념

- `dist[i][j]` = i에서 j로 가는 최단 거리 (2차원 테이블)
- **k번 노드를 거쳐 가는 게 더 짧은지** 모든 k에 대해 확인
- 점화식 : `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`
- DP의 일종

## 원리

```text
i ─────────────▶ j        그냥 가는 거리
 \             ↗
  ▶─── k ────▶            k를 거쳐 가는 거리
```

- k(거쳐가는 노드)를 **제일 바깥 for문**에 둬야 한다
- 순서가 i, j, k로 바뀌면 틀린 답이 나온다

## 코드

### 11404 플로이드

```python
import sys
input = sys.stdin.readline
INF = float('inf')

n = int(input())
m = int(input())
dist = [[INF] * (n + 1) for _ in range(n + 1)]
for i in range(1, n + 1):
    dist[i][i] = 0

for _ in range(m):
    a, b, c = map(int, input().split())
    dist[a][b] = min(dist[a][b], c)      # 같은 경로가 여러 개 들어옴

for k in range(1, n + 1):                # 거쳐가는 노드가 제일 바깥
    for i in range(1, n + 1):
        for j in range(1, n + 1):
            if dist[i][k] + dist[k][j] < dist[i][j]:
                dist[i][j] = dist[i][k] + dist[k][j]

for i in range(1, n + 1):
    print(*[0 if x == INF else x for x in dist[i][1:]])
```

> 같은 출발-도착 버스가 여러 개 들어오는 경우가 있어서 `min`으로 넣어야 한다. 이거 몰라서 한 번 틀림.
{: .prompt-warning }

## 벨만-포드 (음수 간선 있을 때)

다익스트라가 안 되는 **음수 간선**이 있을 때 쓴다. 모든 간선을 N-1번 반복해서 확인하고,
**N번째에도 갱신이 되면 음수 사이클**이 있다는 뜻.

### 11657 타임머신

```python
import sys
input = sys.stdin.readline
INF = float('inf')

n, m = map(int, input().split())
edges = [tuple(map(int, input().split())) for _ in range(m)]
dist = [INF] * (n + 1)
dist[1] = 0

cycle = False
for i in range(n):
    for a, b, c in edges:
        if dist[a] != INF and dist[a] + c < dist[b]:
            dist[b] = dist[a] + c
            if i == n - 1:       # n번째에도 갱신 → 음수 사이클
                cycle = True

if cycle:
    print(-1)
else:
    for i in range(2, n + 1):
        print(dist[i] if dist[i] != INF else -1)
```

## 시간복잡도 / 효율성

| 알고리즘 | 시간복잡도 | 쓸 수 있는 N |
| --- | --- | --- |
| 플로이드 워셜 | O(N³) | N ≤ 500 정도 (파이썬은 더 작게) |
| 벨만-포드 | O(VE) | |

- 메모리도 N² 테이블이 필요
- "모든 쌍의 거리", N이 작다(100 ~ 400) → 플로이드

## 관련 문제
- 11404 플로이드
- 11403 경로 찾기
- 1389 케빈 베이컨의 6단계 법칙
- 11657 타임머신