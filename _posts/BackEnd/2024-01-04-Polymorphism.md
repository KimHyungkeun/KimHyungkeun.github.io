---
title: 클래스와 객체
date: 2024-01-04 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

## 1. 다형성 (Polymorphism)

- 한 객체가 여러 가지 타입을 가질 수 있는 것
- 부모 클래스 타입의 참조 변수로 자식클래스 인스턴스 참조

```java
class Person {}
class Student extends Person {}

Person p1 = new Student();
// Student s1 = new Person(); // 자식이 부모 클래스타입을 역으로 받는건 안됨
```

## 2. instanceof

- 실제 참조하고 있는 인스턴스의 타입 확인

```java
class Person {}
class Student extends Person {}

Person p1 = new Student();
System.out.println(p1 instanceof Person);
```

**(실습 1 : 다형성)**

```java
class Person {
    public void print() {
        System.out.println("Person.print");
    }
}

class Student extends Person{
    public void print() {
        System.out.println("Student.print");
    }

    public void print2() {
        System.out.println("Student.print2");
    }
}

class CollegeStudent extends Person {
    public void print() {
        System.out.println("CollegeStudent.print");
    }
}

public class Main {
    public static void main(String[] args) {
        // 1. 다형성
        System.out.println("== 다형성 ==");
        Person p1 = new Person();
        Student s1 = new Student();

        Person p2 = new Student();
//        Student s2 = new Person(); // 안되는 방식

        p1.print();
        s1.print();
        s1.print2();
        p2.print();

				Person p3 = new CollegeStudent();
//        CollegeStudent c1 = new Student(); // 같은 부모를 상속했어도 형제끼리는 안됨
        p3.print();
    }
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/dedcf376-3c7d-49ea-a763-3f00658de982/Untitled.png)

**(실습 2 : 캐스팅)**

- 업캐스팅 : 객체에 상위 부모의 타입을 넣는 경우
- 다운캐스팅 : 상위 부모 타입을 부여받은 객체에 대해 다시 본인 타입을 캐스팅

```java

class Person {
    public void print() {
        System.out.println("Person.print");
    }
}

class Student extends Person{
    public void print() {
        System.out.println("Student.print");
    }

    public void print2() {
        System.out.println("Student.print2");
    }
}

class CollegeStudent extends Person {
    public void print() {
        System.out.println("CollegeStudent.print");
    }
}

public class Main {
    public static void main(String[] args) {
       // 2. 타입 변환
        System.out.println("== 타입 변환 ==");
        Person pp1 = null;
        Student ss1 = null;

        Person pp2 = new Person();
        Student ss2 = new Student();
        Person pp3 = new Student(); // 업캐스팅

        pp1 = pp2;
        pp1 = ss2;

        ss1 = ss2;
//        ss1 = pp2;
        ss1 = (Student)pp3; // 다운캐스팅
    }
}
```

(실습3 : instanceof)

```java
public class Main {
    public static void main(String[] args) {
       // 3. instanceof
        System.out.println("== instanceof ==");
        Person pe1 = new Person();
        Student st1 = new Student();
        Person pe2 = new Student();
        Person pe3 = new CollegeStudent();

        System.out.println(pe1 instanceof Person);
        System.out.println(pe1 instanceof Student);

        System.out.println(st1 instanceof Student);
        System.out.println(st1 instanceof Person);

        System.out.println(pe2 instanceof Student);
        System.out.println(pe2 instanceof Person);

        System.out.println(pe3 instanceof Student);
        System.out.println(pe3 instanceof Person);

				if (pe1 instanceof Student) {
            Student stu1 = (Student) pe1;
        }

        if (st1 instanceof Person) {
            Person per1 = (Person) st1;
        }
    }
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/71db0cd2-b68c-43fd-a554-2ed12c21c468/Untitled.png)