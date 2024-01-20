---
title: 내부클래스 (Inner Class)
date: 2024-01-05 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

## 1. 내부 클래스 (Inner Class)

- 클래스 in 클래스 (클래스 안에 선언한 클래스

```java
class Outer {
	 ...
	 class Inner {
		...
	 }
}
```

## 2. 내부 클래스 특징

- 내부 클래스에서 외부 클래스 멤버에 접근가능
- 외부에서는 내부 클래스에 접근 가능

## 3. 내부 클래스 종류

- 인스턴스 클래스 (instance class)
- 정적 클래스 (static class)
- 지역 클래스 (local class)
- 익명 클래스 (anonymous class)

## 4. 익명 클래스 (Anonymous Class)

- 이름을 가지지 않는 클래스
- 선언과 동시에 객체 생성
- 일회용 클래스

```java
클래스이름 참조변수이름 = new 클래스 이름() {
  ...
}
```

(실습1 : 외부클래스)

```java
// Java 프로그래밍 - 클래스와 객체_1

// 추상 클래스 Person
class Outer {
    public void print() {
        System.out.println("Outer.Print");
    }

    class Inner {
        public void innerPrint() {
            Outer.this.print();
        }
    }
}
public class Main {
    public static void main(String[] args) {
        
        // 외부 클래스
        Outer o1 = new Outer();
        Outer.Inner i1 = new Outer().new Inner();

    }
}
```

**(실습2: 내부 클래스 - 인스턴스)**

```java
// Java 프로그래밍 - 클래스와 객체_1

// 추상 클래스 Person
class Outer {
    class Inner {
        public void innerPrint() {
            Outer.this.print();
        }
    }
}
public class Main {
    public static void main(String[] args) {
        
        // 내부 클래스 - 인스턴스
        Outer.Inner i1 = new Outer().new Inner();
    }
}
```

**(실습3: 내부 클래스 - 정적 )**

- static이면 바로 메모리에 올라오는 방식이므로,
- Outer.this.print() 사용을 하려해도, Outer는 일반 인스턴스 클래스이기에 서로 타입이 다르다

```java
// Java 프로그래밍 - 클래스와 객체_1

// 추상 클래스 Person
class Outer {
    static class InnerStaticClass {
        void innerPrint() {
//            Outer.this.print();
        }
    }
}
public class Main {
    public static void main(String[] args) {
        
       // 내부 클래스 - 정적
        Outer.InnerStaticClass iis1 = new Outer.InnerStaticClass();
    }
}
```

**(실습4: 익명 클래스)**

```java
abstract class Person {
    public abstract void printInfo();
}

class Student extends Person {
    @Override
    public void printInfo() {
        System.out.println("Student.printInfo");
    }
}

public class Main {
    public static void main(String[] args) {
        
       // 익명 클래스
        Person p1 = new Person() {
            @Override
            public void printInfo() {
                System.out.println("Main.printInfo");
            }
        };
    }
}
```