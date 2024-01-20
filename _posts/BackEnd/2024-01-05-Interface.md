---
title: 인터페이스(Interface)
date: 2024-01-05 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

## 1. 인터페이스

- 다중 상속처럼 사용할 수 있는 기능
- 추상 메소드와 상수만으로 이루어짐

```java
접근제어자 interface 인터페이스 이름 {
	public static final 타입 상수이름 = 값;
	public abstract 반환타입 메소드이름(매개변수);
}

class 클래스 이름 implements 인터페이스 이름 {
 ...
}
```

```java
접근제어자 interface 인터페이스 이름 {
	...
}

접근제어자 class 클래스 이름 {
...
}

class 클래스이름 extends 클래스이름 implements 인터페이스 이름 {
...
}
```

**(실습1 : 인터페이스)**

```java
// Java 프로그래밍 - 클래스와 객체_1

// 추상 클래스 Person
interface School {
    public static final int MAX_CLASS = 20;
    public static final int MAX_PERSON_PER_CLASS = 40;
    public abstract void printSchool();
}

class Student implements School {
    @Override
    public void printSchool() {
        System.out.println("00 University");
    }
}

public class Main {
    public static void main(String[] args) {
        // 1. 인터페이스 기본 사용
        System.out.println("== 인터페이스 기본 사용 ==");
        Student s1 = new Student();
        s1.printSchool();
        System.out.println(s1.MAX_CLASS);
        System.out.println(s1.MAX_PERSON_PER_CLASS);

    }
}
```

<img width="140" alt="Untitled" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/a0945160-ebe6-493e-8caa-c673298500f1">


**(실습2 : 다중상속처럼 사용)**

```java
// Java 프로그래밍 - 클래스와 객체_1

// 추상 클래스 Person
interface School {
    public static final int MAX_CLASS = 20;
    public static final int MAX_PERSON_PER_CLASS = 40;
    public abstract void printSchool();
}

class Person {
    public String name;

    public void printName() {
        System.out.println("Name: " + name);
    }
}

class Student2 extends Person implements School {
    Student2(String name) {
        super.name = name;
    }

    public void printSchool() {
        System.out.println("11 University");
    }
}
public class Main {
    public static void main(String[] args) {

        // 2. 다중 상속처럼 사욯하기
        System.out.println("== Like 다중 상속 ==");
        Student2 s2 = new Student2("A");
        s2.printSchool();
        s2.printName();

    }
}
```

<img width="125" alt="Untitled 1" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/fcf4e478-2023-413b-a67f-d1e96fecce3c">
