---
title: "[코테] 재귀 (Recursion)"
date: 2023-01-25 22:20:00 +0900
categories: [Algorithm, 기법]
tags: [python, baekjoon, 재귀]
---

## 간단한 소개

함수가 자기 자신을 다시 부르는 것.
백트래킹, DFS, DP(탑다운), 분할정복이 전부 재귀로 짜니까 여기서 확실히 잡고 가야 한다.

## 개념

재귀 함수는 무조건 두 부분으로 나뉜다.

1. **종료 조건 (base case)** : 더 이상 안 부르고 끝나는 조건
2. **재귀 호출** : 문제를 더 작게 만들어서 자기 자신 호출

종료 조건이 없으면 무한 호출 → `RecursionError`

## 원리

호출할 때마다 스택에 쌓이고, 끝나면 위에서부터 하나씩 돌아온다.

```text
fact(3)
 └ 3 * fact(2)
        └ 2 * fact(1)
               └ 1          ← 종료 조건
        = 2
 = 6
```

피보나치를 그냥 재귀로 짜면 같은 값을 계속 다시 계산한다.

```text
              fib(5)
          /          \
      fib(4)         fib(3)
      /    \         /    \
  fib(3) fib(2)  fib(2) fib(1)
   ...
```

`fib(3)`, `fib(2)`가 여러 번 계산됨 → 이걸 저장해서 해결하는 게 **DP**.

## 코드

### 팩토리얼

```python
def fact(n):
    if n <= 1:            # 종료 조건
        return 1
    return n * fact(n - 1)
```

### 재귀 깊이 제한 풀기

```python
import sys
sys.setrecursionlimit(10 ** 6)
```

> 파이썬 기본 재귀 깊이는 1000. DFS 문제에서 이거 안 써서 런타임 에러 많이 남.
{: .prompt-warning }

### 11729 하노이 탑

"n-1개를 옆으로 치우고, 제일 큰 걸 옮기고, n-1개를 다시 위로" 라고 생각하면 된다.

```python
def hanoi(n, start, mid, end):
    if n == 1:
        moves.append(f'{start} {end}')
        return
    hanoi(n - 1, start, end, mid)     # n-1개를 중간으로
    moves.append(f'{start} {end}')    # 제일 큰 원판 이동
    hanoi(n - 1, mid, start, end)     # n-1개를 목적지로

n = int(input())
moves = []
hanoi(n, 1, 2, 3)
print(len(moves))
print('\n'.join(moves))
```

재귀는 **"작은 문제가 이미 풀렸다고 믿고"** 짜야 한다는 말이 제일 도움됐다.

## 시간복잡도 / 효율성

| 문제 | 호출 횟수 | 시간복잡도 |
| --- | --- | --- |
| 팩토리얼 | n번 | O(N) |
| 피보나치 (그냥 재귀) | 2배씩 증가 | O(2ⁿ) |
| 하노이 탑 | 2ⁿ - 1번 | O(2ⁿ) |

- 함수 호출 자체가 반복문보다 느리다
- 깊이가 깊으면 메모리도 많이 씀 → 가능하면 반복문으로 바꾸는 것도 생각하기

## 관련 문제
- 10872 팩토리얼
- 10870 피보나치 수 5
- 11729 하노이 탑 이동 순서
- 1074 Z
- 2447 별 찍기 - 10