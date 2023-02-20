---
title: "[코테] 이분 탐색 / 매개변수 탐색"
date: 2023-02-20 22:30:00 +0900
categories: [Algorithm, 탐색]
tags: [python, baekjoon, 이분탐색]
---

## 간단한 소개

정렬된 데이터에서 범위를 **절반씩 줄여가며** 찾는 방법.
처음엔 "값 찾기"로만 생각했는데, 진짜 중요한 건 **"최대/최소를 구하는 문제를 이분 탐색으로 푸는 것(매개변수 탐색)"** 이었다.

## 개념

- **이분 탐색** : 정렬된 배열에서 가운데 값과 비교해서 반을 버린다
- **매개변수 탐색 (Parametric Search)** : "조건을 만족하는 최댓값은?" 같은 최적화 문제를
  **"이 값이면 가능한가? (Yes / No)"** 로 바꿔서 답 자체를 이분 탐색한다
- 반드시 **정렬된 상태**여야 한다

## 원리

![이분 탐색](/assets/img/posts/algorithm/binary-search.png){: width="600" }
_이분 탐색으로 13 찾기_

```text
target = 7
[1, 3, 5, 7, 9, 11, 13]
 lo       mid         hi    → 7 == 7 찾음

target = 11
[1, 3, 5, 7, 9, 11, 13]
 lo       mid         hi    → 7 < 11 → 왼쪽 버림
             lo  mid  hi    → 11 == 11 찾음
```

매개변수 탐색은 답의 범위를 놓고 `가능 / 불가능`이 한 번만 바뀌는 지점을 찾는 것.

```text
절단 높이 :  0  1  2 ... 14  15  16  17  18 ...
얻는 나무 :  O  O  O ...  O   O   X   X   X     ← O인 것 중 최대 = 답
```

## 코드

### 기본 템플릿

```python
def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
```

### bisect 라이브러리

```python
from bisect import bisect_left, bisect_right

arr = [1, 2, 2, 2, 5]
bisect_left(arr, 2)    # 1 : 2가 처음 나오는 위치
bisect_right(arr, 2)   # 4 : 2 다음 위치

# 값이 x인 개수 (10816 숫자 카드 2)
def count(arr, x):
    return bisect_right(arr, x) - bisect_left(arr, x)
```

### 매개변수 탐색 - 2805 나무 자르기

```python
n, m = map(int, input().split())
trees = list(map(int, input().split()))

lo, hi = 0, max(trees)
ans = 0
while lo <= hi:
    mid = (lo + hi) // 2
    total = sum(t - mid for t in trees if t > mid)
    if total >= m:      # 충분함 → 더 높여보기
        ans = mid
        lo = mid + 1
    else:               # 부족함 → 낮추기
        hi = mid - 1
print(ans)
```

> Python3는 시간초과, PyPy3로 통과
{: .prompt-tip }

### 1654 랜선 자르기

같은 패턴. 길이는 1부터 시작해야 0으로 나누기 에러가 안 난다.

```python
import sys
input = sys.stdin.readline

k, n = map(int, input().split())
cables = [int(input()) for _ in range(k)]

lo, hi = 1, max(cables)
ans = 0
while lo <= hi:
    mid = (lo + hi) // 2
    if sum(c // mid for c in cables) >= n:
        ans = mid
        lo = mid + 1
    else:
        hi = mid - 1
print(ans)
```

## 시간복잡도 / 효율성

| | 시간복잡도 |
| --- | --- |
| 선형 탐색 | O(N) |
| 이분 탐색 | **O(log N)** |
| 매개변수 탐색 | O(N log M) (M = 답의 범위) |

- 10억 범위도 30번이면 끝난다 (2³⁰ ≈ 10억)
- "최댓값 / 최솟값", 범위가 **엄청 큼(10억)** → 매개변수 탐색 의심하기

## 관련 문제
- 1920 수 찾기
- 10816 숫자 카드 2
- 2805 나무 자르기
- 1654 랜선 자르기
- 2110 공유기 설치