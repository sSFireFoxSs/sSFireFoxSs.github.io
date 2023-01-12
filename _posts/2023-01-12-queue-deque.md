---
title: "[코테] 큐 (Queue) / 덱 (Deque)"
date: 2023-01-12 23:10:00 +0900
categories: [Algorithm, 자료구조]
tags: [python, baekjoon, 자료구조, 큐, 덱]
---

## 간단한 소개

먼저 들어온 게 먼저 나가는 구조. 줄 서기랑 똑같다.
**BFS는 무조건 큐**를 쓰니까 확실히 알아둬야 한다.

## 개념

- **큐** : 선입선출 (FIFO, First In First Out). 뒤로 넣고 앞에서 뺀다.
- **덱** : 양쪽 끝에서 다 넣고 뺄 수 있는 큐
- 파이썬은 `collections.deque` 하나로 둘 다 해결

## 원리

```text
         앞(front)                뒤(rear)
 빼기 ←  [ 1 ][ 2 ][ 3 ][ 4 ]  ← 넣기

 덱:  ↔  [ 1 ][ 2 ][ 3 ][ 4 ]  ↔
```

## 코드

### 기본 사용

```python
from collections import deque

q = deque()
q.append(1)       # 뒤에 넣기
q.append(2)
q[0]              # 맨 앞 확인 → 1
q.popleft()       # 앞에서 빼기 → 1

dq = deque([1, 2, 3])
dq.appendleft(0)  # 앞에 넣기
dq.pop()          # 뒤에서 빼기
dq.rotate(1)      # 오른쪽으로 한 칸 회전 (-1이면 왼쪽)
```

> 리스트로 `pop(0)` 하면 O(N)이라 큐로 쓰면 시간초과. 무조건 `deque.popleft()`
{: .prompt-warning }

### 2164 카드2

```python
from collections import deque

n = int(input())
q = deque(range(1, n + 1))

while len(q) > 1:
    q.popleft()              # 맨 위 버리기
    q.append(q.popleft())    # 그다음 카드 맨 아래로

print(q[0])
```

### 1158 요세푸스 - rotate 활용

```python
from collections import deque

n, k = map(int, input().split())
q = deque(range(1, n + 1))
ans = []

while q:
    q.rotate(-(k - 1))       # k-1명 뒤로 보내기
    ans.append(q.popleft())

print('<' + ', '.join(map(str, ans)) + '>')
```

### 슬라이딩 윈도우 최솟값 - 11003 최솟값 찾기

길이 L인 구간의 최솟값을 계속 구하는 문제. 덱 앞쪽에 항상 최솟값이 오도록 유지한다.

```python
import sys
from collections import deque
input = sys.stdin.readline

n, l = map(int, input().split())
a = list(map(int, input().split()))
dq = deque()                 # (인덱스, 값)
ans = []

for i in range(n):
    while dq and dq[-1][1] > a[i]:   # 나보다 큰 건 필요 없음
        dq.pop()
    dq.append((i, a[i]))
    if dq[0][0] <= i - l:            # 구간 벗어난 건 제거
        dq.popleft()
    ans.append(dq[0][1])

print(*ans)
```

> 이 문제는 Python3로 내면 시간초과, PyPy3로 통과
{: .prompt-tip }

## 시간복잡도 / 효율성

| 연산 | deque | list |
| --- | --- | --- |
| 뒤에 넣기/빼기 | O(1) | O(1) |
| 앞에 넣기/빼기 | **O(1)** | O(N) |
| 인덱스 접근 (중간) | O(N) | O(1) |
| rotate(k) | O(k) | - |

슬라이딩 윈도우도 각 원소가 한 번씩 들어가고 나오니까 전체 **O(N)**.

## 관련 문제
- 18258 큐 2
- 10866 덱
- 2164 카드2
- 1158 요세푸스 문제
- 1966 프린터 큐
- 11003 최솟값 찾기