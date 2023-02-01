---
title: "[코테] 백트래킹 (Backtracking) - N과 M, N-Queen"
date: 2023-02-01 23:20:00 +0900
categories: [Algorithm, 기법]
tags: [python, baekjoon, 백트래킹, 재귀]
---

## 간단한 소개

완전탐색인데 **안 될 것 같은 길은 미리 끊고 돌아가는** 방법.
N과 M 시리즈 풀면서 감 잡았다. 템플릿 하나만 외우면 거의 다 응용된다.

## 개념

- 재귀로 한 칸씩 선택해 나가다가
- 조건에 안 맞으면 더 들어가지 않고 **되돌아온다 (가지치기, Pruning)**
- 되돌아올 때 **선택했던 걸 원래대로 돌려놓는 것**이 핵심

## 원리

1~3 중에서 2개 뽑는 순열을 트리로 그리면

```text
              (시작)
       /        |        \
      1         2         3
    /   \     /   \     /   \
   2     3   1     3   1     2     ← 길이 2 되면 출력하고 돌아감
```

- 들어갈 때 : 선택 + 방문 표시
- 나올 때 : 선택 취소 + 방문 해제

## 코드

### 기본 템플릿 - 15649 N과 M (1)

```python
n, m = map(int, input().split())
seq = []
used = [False] * (n + 1)

def bt():
    if len(seq) == m:            # 종료 조건
        print(*seq)
        return
    for i in range(1, n + 1):
        if used[i]:
            continue
        used[i] = True           # 선택
        seq.append(i)
        bt()
        seq.pop()                # 되돌리기
        used[i] = False

bt()
```

### N과 M 시리즈 차이

| 문제 | 조건 | 바꿀 부분 |
| --- | --- | --- |
| 15649 (1) | 순열 | 기본 템플릿 |
| 15650 (2) | 조합 (오름차순) | `bt(start)`로 받아서 `range(start, n+1)` |
| 15651 (3) | 중복 순열 | `used` 체크 없애기 |
| 15652 (4) | 중복 조합 | `bt(i)`로 자기 자신부터 다시 |

### 9663 N-Queen

![8-Queen](/assets/img/posts/algorithm/nqueen.png){: width="360" }
_8-Queen 해 중 하나_

같은 열, 두 대각선에 퀸이 있는지 배열로 체크해서 바로 가지치기.

```python
n = int(input())
col = [False] * n
diag1 = [False] * (2 * n - 1)    # r + c 같은 대각선
diag2 = [False] * (2 * n - 1)    # r - c 같은 대각선
cnt = 0

def solve(r):
    global cnt
    if r == n:
        cnt += 1
        return
    for c in range(n):
        if col[c] or diag1[r + c] or diag2[r - c + n - 1]:
            continue                       # 가지치기
        col[c] = diag1[r + c] = diag2[r - c + n - 1] = True
        solve(r + 1)
        col[c] = diag1[r + c] = diag2[r - c + n - 1] = False

solve(0)
print(cnt)
```

> 9663은 Python3로는 시간초과, PyPy3로도 아슬아슬하다.
{: .prompt-warning }

## 시간복잡도 / 효율성

- 최악은 완전탐색이랑 같다 (순열 O(N!), 부분집합 O(2ⁿ))
- 실제로는 가지치기를 얼마나 잘 하냐에 따라 속도가 크게 달라진다
- 조건 체크를 `in list`로 하면 느림 → **boolean 배열**로 O(1) 체크

## 관련 문제
- 15649 ~ 15652 N과 M (1) ~ (4)
- 6603 로또
- 14888 연산자 끼워넣기
- 9663 N-Queen