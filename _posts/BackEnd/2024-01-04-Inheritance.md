---
title: 상속 (Inheritance)
date: 2024-01-04 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

## 1. 상속 (Inheritance)

- 기존 클래스에 기능 추가 및 재정의 하여 새로운 클래스를 정의
    - 부모 클래스 : 상속 대상이 되는 기존 클래스 (상위 클래스, 기초 클래스)
    - 자식 클래스 : 기존 클래스를 상속하는 클래스 (하위 클래스, 파생 클래스)
- 부모 클래스의 필드와 메소드가 상속됨 (Generator, 초기화 블록은 상속이 안됨)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/0bbfe4aa-9073-4fec-b35f-8e397f182d9e/Untitled.png)

- 다중 상속은 불가능
- private, default 멤버는 자식 클래스에서 접근 불가
    
    ⇒ default의 경우, 내부 패키지의 자식 클래스는 가능
    

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/f80e62f0-58ea-46f0-a77a-386dc25e5289/Untitled.png)

## 2. super, super()

- super
    - 부모 클래스와 자식 클래스의 멤버 이름이 같을 때 구분하는 키워드
- super()
    - 부모 클래스의 생성자 호출

## 3. 오버라이딩 (Overriding)

- 부모 클래스의 메소드를 자식 클래스에서 재정의
- 오버라이딩 조건
    - 메소드의 선언부는 부모 클래스의 메소드와 동일
    - 반환 타입에 한해, 부모 클래스의 반환 타입으로 변환할 수 있는 타입으로 변경 가능
    - 부모 클래스의 메소드보다 접근제어자를 더 좁은 범위로 변경 불가
    - 부모 클래스의 메소드보다 더 큰 범위의 예외 선언 불가

(실습 : 상속 및 오버라이딩)

```java
// Java 프로그래밍 - 클래스와 객체_1

class Person {
    String name;
    int age;
    public int a1;
    private int a2;

    Person() {}
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void printInfo() {
        System.out.println("Person.printInfo");
        System.out.println("name : " + name);
        System.out.println("age: " + age);
    }
}

// Student 클래스 - Person 상속, 접근제어자 확인
class Student extends Person {
    Student() {
        a1 = 1;
//        a2 = 1; // Person의 a2가 private 이므로 Person 클래스 내에서만 접근 가능
    }
}

// Student2 클래스 - Person 상속, super 사용, 접근제어자 확인
class Student2 extends Person {
    String name;
    int stdId;
    Student2(String name, int age, int stdId) {
        this.name = name;
//        super.name = name;
//        super(name, age);
        this.age = age;
        this.stdId = stdId;
    }

    public void printInfo() {
        System.out.println("Student2.printInfo");
        System.out.println("name : " + name);
        System.out.println("age: " + age);
        System.out.println("stdId : " + stdId);

    }
}
public class Main {
    public static void main(String[] args) {
//        1. 상속
        System.out.println("=========");
        Student s1 = new Student();
        s1.name = "a";
        s1.age = 25;
        s1.printInfo();

//        2. super, super(), 오버라이딩
        System.out.println("==========");
        Student2 s2 = new Student2("b",32,1);
        s2.printInfo();
    }
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/aa5027ec-e2f0-41aa-a33a-36b36a992fac/Untitled.png)

(실습 2 : Student2의 name이 null이 나오는 이유)

```java
class Person {
    String name;
    int age;
    public int a1;
    private int a2;

    Person() {}
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void printInfo() {
        System.out.println("Person.printInfo");
        System.out.println("name : " + name);
        System.out.println("age: " + age);
    }
}

// Student2 클래스 - Person 상속, super 사용, 접근제어자 확인
class Student2 extends Person {
    String name;
    int stdId;
    Student2(String name, int age, int stdId) {
//       this.name = name;
//        super.name = name;
        super(name, age);
        this.age = age;
        this.stdId = stdId;
    }

    public void printInfo() {
        System.out.println("Student2.printInfo");
        System.out.println("name : " + name);
        System.out.println("age: " + age);
        System.out.println("stdId : " + stdId);
    }
}

public class Main {
    public static void main(String[] args) {

//        2. super, super(), 오버라이딩
        System.out.println("==========");
        Student2 s2 = new Student2("b",32,1);
        s2.printInfo();
    }
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/3fb7a306-58cf-4f4c-b0a2-2f43a8f74887/Untitled.png)

- Student2의 super(name, age)에 의해 Person(부모 클래스)의 name에 값이 들어간다
- 이로 인해 Student2의 name은 따로 값이 들어가지 않았기에 null값이 나온다