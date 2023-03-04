---
title: "[코테] 다이나믹 프로그래밍 (DP)"
date: 2023-03-04 23:55:00 +0900
categories: [Algorithm, 기법]
tags: [python, baekjoon, dp]
---

## 간단한 소개

코테 공부하면서 제일 오래 붙잡고 있었던 유형.
코드는 짧은데 **점화식을 세우는 게** 어렵다. 백준 실버 문제 위주로 풀면서 정리했다.

## 개념

- 큰 문제를 작은 문제로 나누고, **작은 문제의 답을 저장해서 다시 계산하지 않는** 방법
- 쓸 수 있는 조건
  1. **최적 부분 구조** : 큰 문제의 답을 작은 문제의 답으로 만들 수 있다
  2. **중복되는 부분 문제** : 같은 작은 문제가 여러 번 나온다
- 구현 방법 두 가지

| | 탑다운 (Top-Down) | 바텀업 (Bottom-Up) |
| --- | --- | --- |
| 방식 | 재귀 + 메모이제이션 | 반복문 + DP 테이블 |
| 장점 | 점화식 그대로 옮기기 쉬움 | 재귀 깊이 걱정 없음, 빠름 |
| 코테 | 가끔 | **주로 이거** |

## 원리

피보나치를 그냥 재귀로 하면 같은 계산이 계속 반복된다. (재귀 글 참고)

```text
fib(5) → fib(4), fib(3)
fib(4) → fib(3), fib(2)     ← fib(3) 또 계산
...
```

한 번 계산한 값을 `d[i]`에 저장해두면 각 값은 한 번만 계산된다.

### 문제 푸는 순서 (내가 정한 것)

1. `d[i]`가 **무엇을 의미하는지** 한국어로 먼저 적기
2. `d[i]`를 이전 값들로 어떻게 만드는지 (**점화식**)
3. 초기값 (`d[0]`, `d[1]`)
4. 반복문 방향 (작은 것부터)

## 코드

### 피보나치 - 탑다운 / 바텀업

```python
# 탑다운
import sys
sys.setrecursionlimit(10 ** 6)
memo = {}
def fib(n):
    if n <= 2:
        return 1
    if n in memo:
        return memo[n]
    memo[n] = fib(n - 1) + fib(n - 2)
    return memo[n]

# 바텀업
d = [0] * (n + 1)
d[1] = d[2] = 1
for i in range(3, n + 1):
    d[i] = d[i - 1] + d[i - 2]
```

### 1463 1로 만들기

`d[i]` = i를 1로 만드는 최소 연산 횟수

```python
n = int(input())
d = [0] * (n + 1)

for i in range(2, n + 1):
    d[i] = d[i - 1] + 1                  # 1 빼기
    if i % 2 == 0:
        d[i] = min(d[i], d[i // 2] + 1)  # 2로 나누기
    if i % 3 == 0:
        d[i] = min(d[i], d[i // 3] + 1)  # 3으로 나누기
print(d[n])
```

처음에 그리디로 "3으로 나눠지면 무조건 나누기"로 했다가 틀림. 10 → 9 → 3 → 1 이 더 짧다.

### 2579 계단 오르기

`d[i]` = i번째 계단을 **밟았을 때** 최대 점수. 연속 세 칸 금지라서 경우가 두 개.

```python
n = int(input())
s = [0] + [int(input()) for _ in range(n)]
d = [0] * (n + 1)

d[1] = s[1]
if n >= 2:
    d[2] = s[1] + s[2]
for i in range(3, n + 1):
    d[i] = max(d[i - 2], d[i - 3] + s[i - 1]) + s[i]
print(d[n])
```

### 2294 동전 2

`d[x]` = x원을 만드는 최소 동전 개수. 그리디로 안 되던 그 문제.

```python
import sys
input = sys.stdin.readline

n, k = map(int, input().split())
coins = [int(input()) for _ in range(n)]
INF = 10001
d = [INF] * (k + 1)
d[0] = 0

for c in coins:
    for x in range(c, k + 1):
        d[x] = min(d[x], d[x - c] + 1)

print(d[k] if d[k] != INF else -1)
```

### 12865 평범한 배낭 (0-1 냅색)

`d[w]` = 무게 w 이하로 담을 수 있는 최대 가치. 물건을 한 번만 써야 해서 **뒤에서부터** 돈다.

```python
n, k = map(int, input().split())
d = [0] * (k + 1)

for _ in range(n):
    w, v = map(int, input().split())
    for x in range(k, w - 1, -1):
        d[x] = max(d[x], d[x - w] + v)
print(d[k])
```

> 앞에서부터 돌면 같은 물건을 여러 번 넣는 게 돼버린다. (그건 동전 문제처럼 무한 개일 때)
{: .prompt-warning }

### 11053 가장 긴 증가하는 부분 수열 (LIS)

`d[i]` = i번째 수로 **끝나는** LIS 길이

```python
n = int(input())
a = list(map(int, input().split()))
d = [1] * n

for i in range(n):
    for j in range(i):
        if a[j] < a[i]:
            d[i] = max(d[i], d[j] + 1)
print(max(d))
```

## 시간복잡도 / 효율성

| 문제 | 시간복잡도 |
| --- | --- |
| 피보나치 (DP) | O(N) (그냥 재귀는 O(2ⁿ)) |
| 1로 만들기 | O(N) |
| 동전 2 | O(N × K) |
| 배낭 | O(N × K) |
| LIS (기본) | O(N²) (이분 탐색 쓰면 O(N log N)) |

- 상태 개수 × 상태 하나 계산 비용 = 전체 시간
- 메모리도 상태 개수만큼 쓴다

> "최대 / 최소 / 경우의 수" + 그리디 반례가 있음 → DP 의심하기
{: .prompt-tip }

## 관련 문제
- 1463 1로 만들기
- 9095 1, 2, 3 더하기
- 2579 계단 오르기
- 1932 정수 삼각형
- 2294 동전 2
- 12865 평범한 배낭
- 11053 가장 긴 증가하는 부분 수열