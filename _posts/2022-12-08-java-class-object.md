---
title: "[Java] 클래스와 객체 - 생성자, 접근 제어자, static"
date: 2022-12-08 23:30:00 +0900
categories: [Java]
tags: [java, oop]
---

객체지향 수업 과제(은행 계좌 프로그램) 하다가 생성자랑 static에서 계속 막혀서 제대로 정리해 본다.
다른 블로그 몇 개랑 교재 보면서 정리한 내용이다.

## 1. 클래스와 객체

- **클래스** : 객체를 만들기 위한 설계도
- **객체(인스턴스)** : 설계도로 실제로 만든 것

붕어빵 틀이 클래스, 붕어빵이 객체라는 비유가 제일 와닿았다.

```java
public class Car {
    String name;   // 필드
    int speed;

    void accel() { // 메서드
        speed += 10;
    }
}
```

```java
Car a = new Car();
Car b = new Car();

a.name = "소나타";
a.accel();

System.out.println(a.speed); // 10
System.out.println(b.speed); // 0  → a랑 b는 서로 다른 객체
```

## 2. 생성자

객체를 만들 때(`new`) 자동으로 호출되는 메서드.
- 이름이 클래스 이름이랑 같다
- 반환 타입이 없다 (`void`도 안 씀)

```java
public class Car {
    String name;
    int speed;

    public Car(String name) {
        this.name = name;
        this.speed = 0;
    }
}

Car car = new Car("소나타");
```

### this

`this`는 객체 자기 자신을 가리킨다.
매개변수 이름이랑 필드 이름이 같을 때 구분하려고 쓴다.

```java
public Car(String name) {
    name = name;       // X - 매개변수에 자기 자신 대입 (필드 안 바뀜)
    this.name = name;  // O
}
```

### 기본 생성자

```java
public class Car {
    String name;

    public Car(String name) {
        this.name = name;
    }
}

Car car = new Car();  // 컴파일 에러!
```

> 생성자를 하나도 안 만들면 기본 생성자 `Car()`를 자동으로 만들어 주지만,
> **하나라도 직접 만들면 기본 생성자는 안 만들어 준다.**
{: .prompt-warning }

이거 때문에 과제에서 30분 날렸다.

### 생성자 오버로딩 + this()

```java
public class Car {
    String name;
    int speed;

    public Car() {
        this("이름없음", 0);  // 다른 생성자 호출
    }

    public Car(String name, int speed) {
        this.name = name;
        this.speed = speed;
    }
}
```

`this()`는 생성자 **첫 줄**에만 쓸 수 있다.

## 3. 접근 제어자

| 제어자 | 같은 클래스 | 같은 패키지 | 자식 클래스 | 전체 |
| --- | :---: | :---: | :---: | :---: |
| public | O | O | O | O |
| protected | O | O | O | X |
| (default) | O | O | X | X |
| private | O | X | X | X |

## 4. 캡슐화 (getter / setter)

필드를 `private`으로 막고 메서드로만 접근하게 하는 것.
값을 아무렇게나 바꾸지 못하게 막을 수 있다.

```java
public class Account {
    private String owner;
    private int balance;

    public Account(String owner) {
        this.owner = owner;
    }

    public int getBalance() {
        return balance;
    }

    public void deposit(int money) {
        if (money <= 0) {
            System.out.println("입금액이 올바르지 않습니다.");
            return;
        }
        balance += money;
    }

    public void withdraw(int money) {
        if (money > balance) {
            System.out.println("잔액 부족");
            return;
        }
        balance -= money;
    }
}
```

`account.balance = -1000;` 같은 걸 막을 수 있음. 과제 요구사항이 딱 이거였다.

## 5. static

`static`이 붙으면 **객체마다 따로 있는 게 아니라 클래스에 하나만** 존재한다.

```java
public class Account {
    static int count = 0;   // 전체 계좌 수
    private int balance;    // 계좌마다 다름

    public Account() {
        count++;
    }
}

new Account();
new Account();
System.out.println(Account.count); // 2
```

- `static` 변수/메서드는 `객체.이름` 말고 `클래스.이름`으로 쓴다
- `static` 메서드 안에서는 일반 필드(인스턴스 변수)를 못 쓴다 → 객체가 없을 수도 있으니까

```java
public static void main(String[] args) {
    balance = 100;  // 에러: static 메서드에서 인스턴스 변수 사용 불가
}
```

`main`이 왜 `static`인지 이제야 이해함. 객체 안 만들고 바로 실행해야 하니까.

## 정리

| 개념 | 한 줄 |
| --- | --- |
| 클래스 / 객체 | 설계도 / 실체 |
| 생성자 | `new` 할 때 호출, 하나라도 만들면 기본 생성자 X |
| this | 자기 자신, `this()`는 다른 생성자 호출 |
| 캡슐화 | private + getter/setter |
| static | 클래스에 하나, 객체 없이 사용 |

## 참고
- [점프 투 자바 - 클래스](https://wikidocs.net/214)
- [Java Tutorials - Classes and Objects](https://docs.oracle.com/javase/tutorial/java/javaOO/index.html)