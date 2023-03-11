---
title: "[코테] 소수 판별 / 에라토스테네스의 체"
date: 2023-03-11 21:10:00 +0900
categories: [Algorithm, 수학]
tags: [python, baekjoon, 소수]
---

## 간단한 소개

소수 문제는 거의 이 두 개로 끝난다.

## 개념

- **소수** : 1과 자기 자신으로만 나누어떨어지는 2 이상의 수
- 수 하나 판별 → **제곱근까지만** 나눠보기
- 범위 안의 소수 전부 → **에라토스테네스의 체**

## 원리

![에라토스테네스의 체](/assets/img/posts/algorithm/sieve.png){: width="480" }
_1 ~ 50 에라토스테네스의 체_

- 약수는 짝을 이룬다 (16 = 2×8 = 4×4). 그래서 √N까지만 보면 된다
- 체 : 2부터 시작해서 **소수의 배수를 전부 지운다**

```text
2  3  4  5  6  7  8  9  10 11 12 13 14 15 16
2  3  ×  5  ×  7  ×  9  ×  11 ×  13 ×  15 ×   ← 2의 배수 지움
2  3  ×  5  ×  7  ×  ×  ×  11 ×  13 ×  ×  ×   ← 3의 배수 지움
```

## 코드

### 하나 판별

```python
def is_prime(x):
    if x < 2:
        return False
    for i in range(2, int(x ** 0.5) + 1):
        if x % i == 0:
            return False
    return True
```

### 에라토스테네스의 체 - 1929 소수 구하기

```python
m, n = map(int, input().split())
is_p = [True] * (n + 1)
is_p[0] = is_p[1] = False

for i in range(2, int(n ** 0.5) + 1):
    if is_p[i]:
        for j in range(i * i, n + 1, i):   # i*i부터 지워도 됨
            is_p[j] = False

print('\n'.join(str(i) for i in range(m, n + 1) if is_p[i]))
```

## 시간복잡도 / 효율성

| 방법 | 시간복잡도 |
| --- | --- |
| 하나 판별 | O(√N) |
| 에라토스테네스의 체 | O(N log log N) |

N이 100만 정도까지는 체가 빠르다. 대신 크기 N 리스트가 필요해서 너무 크면 메모리 문제.

## 관련 문제
- 1978 소수 찾기
- 1929 소수 구하기
- 6588 골드바흐의 추측