---
title: "[코테] 완전탐색 (Brute Force) - 순열, 조합"
date: 2023-01-29 20:45:00 +0900
categories: [Algorithm, 기법]
tags: [python, baekjoon, 완전탐색, 브루트포스]
---

## 간단한 소개

가능한 경우를 **전부 다 해보는** 방법. 무식해 보이지만 N이 작으면 제일 확실하다.

## 개념

- N 보고 판단 : 경우의 수가 대략 **1억 이하**면 해볼 만함
- 파이썬은 `itertools`로 순열/조합을 바로 만들 수 있다

## 원리

| 종류 | 의미 | 개수 |
| --- | --- | --- |
| 순열 | 순서 O, n개 중 r개 | nPr |
| 조합 | 순서 X, n개 중 r개 | nCr |
| 중복 순열 | 같은 거 여러 번 가능 | nʳ |
| 부분집합 | 각각 넣을지 말지 | 2ⁿ |

## 코드

```python
from itertools import permutations, combinations, product

list(permutations([1, 2, 3], 2))   # (1,2) (1,3) (2,1) (2,3) (3,1) (3,2)
list(combinations([1, 2, 3], 2))   # (1,2) (1,3) (2,3)
list(product([0, 1], repeat=3))    # 000 ~ 111
```

### 2798 블랙잭 (카드 3장 합이 M 이하 중 최대)

```python
from itertools import combinations

n, m = map(int, input().split())
cards = list(map(int, input().split()))

ans = 0
for c in combinations(cards, 3):
    s = sum(c)
    if s <= m:
        ans = max(ans, s)
print(ans)
```

### 1182 부분수열의 합

```python
from itertools import combinations

n, s = map(int, input().split())
arr = list(map(int, input().split()))

cnt = 0
for r in range(1, n + 1):
    for c in combinations(arr, r):
        if sum(c) == s:
            cnt += 1
print(cnt)
```

## 시간복잡도 / 효율성

- 순열 O(N!), 부분집합 O(2ⁿ) → N이 10~20 넘어가면 안 됨
- 경우의 수가 너무 많으면 **백트래킹(가지치기)** 으로 줄인다 → 다음에 정리

## 관련 문제
- 2798 블랙잭
- 2231 분해합
- 7568 덩치
- 1018 체스판 다시 칠하기
- 1182 부분수열의 합