---
title: "[Java] 상속 - 헷갈렸던 것만"
date: 2022-12-22 23:52:00 +0900
categories: [Java]
tags: [java, oop]
---

기말 공부하다가 헷갈린 것만 메모.

## 오버라이딩 vs 오버로딩

- **오버라이딩** : 부모 메서드를 자식이 **다시 정의** (이름, 매개변수 같음)
- **오버로딩** : 같은 이름, **매개변수만 다르게** 여러 개

```java
class Animal {
    void sound() { System.out.println("..."); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("멍멍"); }  // 오버라이딩
}
```

## 추상 클래스 vs 인터페이스

| | 추상 클래스 | 인터페이스 |
| --- | --- | --- |
| 키워드 | extends | implements |
| 여러 개 | X | O |
| 언제 | 공통 코드 물려줄 때 | 기능 규칙 강제할 때 |

## super()

자식 생성자 첫 줄에 `super()`가 자동으로 들어간다.
부모에 기본 생성자가 없으면 에러 → 직접 `super(값)` 써줘야 함.

시험 끝나면 코테 준비 시작해야지.