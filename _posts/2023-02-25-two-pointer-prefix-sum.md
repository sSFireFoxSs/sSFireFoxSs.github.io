---
title: "[코테] 투 포인터 / 구간 합"
date: 2023-02-25 14:20:00 +0900
categories: [Algorithm, 탐색]
tags: [python, baekjoon, 투포인터, 구간합]
---

## 간단한 소개

둘 다 **연속된 구간**을 다룰 때 쓰는 방법이라 묶어서 정리.
이중 for문 O(N²)을 O(N)으로 줄여준다.

## 개념

- **투 포인터** : 포인터 두 개(start, end)를 움직이면서 구간을 조절
  - 같은 방향 : 연속 부분합 (2003, 1806)
  - 양 끝에서 : 정렬된 배열에서 두 수의 합 (2470)
- **구간 합 (누적 합, Prefix Sum)** : 앞에서부터 합을 미리 구해두고 `P[r] - P[l-1]`로 바로 계산

## 원리

### 투 포인터 (합이 M인 연속 구간)

```text
M = 5
[1, 2, 3, 2, 5]
 s     e          합 6 > 5 → s 이동
    s  e          합 5 == 5 → 카운트, s 이동
       s  e       ...
```

- 합이 작으면 `end` 늘리기
- 합이 크거나 같으면 `start` 줄이기
- 두 포인터가 **뒤로 가지 않으니까** 전체 O(N)

> 원소가 전부 **양수**일 때만 가능하다. 음수가 있으면 늘려도 합이 줄어들 수 있음.
{: .prompt-warning }

### 구간 합

```text
arr    =    [10, 20, 30, 40, 50]
prefix = [0, 10, 30, 60, 100, 150]

2번째 ~ 4번째 합 = prefix[4] - prefix[1] = 100 - 10 = 90
```

## 코드

### 2003 수들의 합 2

```python
n, m = map(int, input().split())
a = list(map(int, input().split()))

cnt = 0
end = 0
s = 0
for start in range(n):
    while s < m and end < n:
        s += a[end]
        end += 1
    if s == m:
        cnt += 1
    s -= a[start]
print(cnt)
```

### 2470 두 용액 (양 끝에서 좁혀오기)

```python
n = int(input())
a = sorted(map(int, input().split()))

l, r = 0, n - 1
best = float('inf')
ans = (0, 0)
while l < r:
    s = a[l] + a[r]
    if abs(s) < best:
        best = abs(s)
        ans = (a[l], a[r])
    if s < 0:
        l += 1
    else:
        r -= 1
print(*ans)
```

### 11659 구간 합 구하기 4

```python
import sys
input = sys.stdin.readline

n, m = map(int, input().split())
arr = list(map(int, input().split()))

prefix = [0]
for x in arr:
    prefix.append(prefix[-1] + x)

out = []
for _ in range(m):
    i, j = map(int, input().split())
    out.append(prefix[j] - prefix[i - 1])
print('\n'.join(map(str, out)))
```

## 시간복잡도 / 효율성

| 방법 | 시간복잡도 |
| --- | --- |
| 이중 for문으로 모든 구간 | O(N²) |
| 투 포인터 | **O(N)** |
| 구간 합 : 전처리 / 질의 1번 | O(N) / **O(1)** |

질의가 M번이면 구간 합은 O(N + M), 매번 더하면 O(NM).

## 관련 문제
- 2003 수들의 합 2
- 1806 부분합
- 2470 두 용액
- 11659 구간 합 구하기 4
- 2559 수열