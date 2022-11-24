---
title: "[Java] 기본 문법 정리"
date: 2022-11-24 22:10:00 +0900
categories: [Java]
tags: [java]
---

복학하고 자바 다시 시작. 1학년 때 배운 거 거의 다 까먹어서 기본부터 다시 정리한다.

## 자료형

| 구분 | 타입 | 크기 |
| --- | --- | --- |
| 정수 | byte / short / int / long | 1 / 2 / 4 / 8 byte |
| 실수 | float / double | 4 / 8 byte |
| 문자 | char | 2 byte |
| 논리 | boolean | 1 byte |

```java
long big = 10000000000L;  // long은 L 붙이기
float f = 3.14f;          // float은 f 붙이기
String s = "hello";       // String은 기본형 아님 (참조형)
```

## 형변환

```java
int x = 10;
double y = x;          // 자동 형변환
int z = (int) 3.99;    // 강제 형변환 → 3 (버림)

System.out.println(7 / 2);    // 3
System.out.println(7 / 2.0);  // 3.5
```

정수끼리 나누면 정수가 나온다. 과제에서 평균 구하다가 이거 때문에 틀렸었음.

## 조건문 / 반복문

```java
if (score >= 90) {
    grade = 'A';
} else if (score >= 80) {
    grade = 'B';
} else {
    grade = 'C';
}

for (int i = 0; i < 5; i++) {
    System.out.println(i);
}

int[] arr = {1, 2, 3};
for (int n : arr) {   // 향상된 for문
    System.out.println(n);
}
```

switch는 `break` 안 쓰면 아래 case까지 다 실행되니까 주의.

## 문자열 비교

```java
String s1 = "java";
String s2 = new String("java");

s1 == s2;       // false (주소 비교)
s1.equals(s2);  // true  (값 비교)
```

> 문자열 비교는 무조건 `equals()`
{: .prompt-tip }

## 참고
- [점프 투 자바](https://wikidocs.net/book/31)