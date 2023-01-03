---
title: "[코테] 파이썬으로 코테 시작 - 입출력"
date: 2023-01-03 21:15:00 +0900
categories: [Algorithm, 기초]
tags: [python, baekjoon]
---

코테 준비 시작. 자바로 하려다가 코드가 너무 길어져서 파이썬으로 바꿨다.
앞으로 유형별로 하나씩 정리하면서 공부하다 막히면 여기 다시 보려고 한다.

## 템플릿

```python
import sys
input = sys.stdin.readline

n, m = map(int, input().split())
arr = list(map(int, input().split()))
s = input().strip()          # 문자열은 strip() 필수 (끝에 \n 붙어 있음)

grid = [list(map(int, input().split())) for _ in range(n)]
```

- `input = sys.stdin.readline` 한 줄이면 입력 빨라짐
- 문자열 받을 때 `.strip()` 안 하면 개행문자까지 들어옴 → 이거 때문에 틀림

## 출력

```python
print('\n'.join(map(str, result)))
print(*arr)    # 공백으로 구분해서 한 줄 출력
```

> 15552번(빠른 A+B)에서 `input()`으로 내면 시간초과
{: .prompt-warning }