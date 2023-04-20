---
layout: default
title: 1. Class & Interface
parent: Java
nav_order: 1
---

## 1. 객체지향 프로그래밍 (OOP, Object-Oriented Programming)

정의 : 객체 지향 프로그래밍에서는 모든 데이터를 객체(object)로 취급하며 
이러한 객체가 바로 프로그래밍의 중심

- 객체(Object) : 실생활에서 우리가 인식할 수 있는 사물
- 클래스(Class) : 객체를 만들어 내기 위한 설계도 또는 툴
- 인스턴스(Instance) : 실제 메모리에 할당 된 객체

## 2. 클래스 (Class)

- 정의 : 객체를 생성하기 위한 일종의 설계도 개념
- ex) Sample.java

```bash
// Animal이란 이름의 클래스가 존재
// new 연산자를 통해서 cat이라는 객체를 만들었음
// 그리고, cat은 Animal의 Instance라 볼 수 있음
class Animal {
}

public class Sample {
    public static void main(String[] args) {
				Animal cat = new Animal();
    }
}
```

1) 필드(field)

- 클래스 안의 기능을 끄집어 내서 사용할 때 사용

2) 메소드 (method)

- 함수 이며 객체의 행동을 구현

3) 생성자 (Constructor)

- 클래스의 이름과 동일한 메소드
- 객체가 생성될 때 자동으로 호출되는 특수한 메소드

```bash
class A {

	// 멤버 변수
	int a;
	int b;
	  
	// 생성자
	A(){
		this.a = 10;
	  this.b = 15;
	}
	
	// 메소드
	void output() {
	  System.out.println("Hello");
  }
}

public class Test {
	public static void main(String[] args){
    //프로그램 시작 시점 
    }
}
```

```bash
public class Person {

		// 기본 생성자
    Person(){
    	name = "junseo";
        age = 24;
    }
    
    // 생성자 인자(매개변수)가 존재하는 경우
    Person(String name1, int age1){
    	name = name1;
        age = age1;
    }
	
    // 멤버 변수
    int age;
    String name;
    
    // 메서드(1번 경우)
    public void hello(){
    	System.out.println("hello! my name is " + this.name );
    }
    
    // 메서드(2번 경우)
    public void hello(String to){
    	System.out.println("hello! " + to +  " my name is " + this.name );
    }
    
    // 리턴 값이 있는 메서드(3번 경우)
    public String hello1(){
    
    	String hello = "hello! my name is " + this.name; 
    	return hello;
    }
    
    // 리턴 값이 있는 메서드(4번 경우)
    public String hello1(String to){
    
    	String hello = "hello! " + to +  " my name is " + this.name;
    	return hello;
    }
    

}
```

4) 접근 제어자

- 외부의 클래스가 해당 클래스의 접근을 허용하는 범위

**public > protected > default > private**

- public : 같은 클래스 / 같은 패키지 / 자식 클래스 허용 / 그 외 허용
- protected : 같은 클래스 / 같은 패키지 / 자식 클래스 허용
- default : 같은 클래스 / 같은 패키지
- private : 같은 클래스

## 3. 상속

- 정의 : 자식 클래스가 부모 클래스의 기능을 그대로 물려받을 수 있는 기능
- 클래스 상속을 위해 extends 라는 키워드를 사용한다.
- ex) Animal.java

```java
// Animal이라는 부모 클래스가 존재

// sleep()은 Dog 클래스의 메소드이므로 사용 가능

class Animal {
    String name;
		
	// setName 내에서 this.name을 통해 지정되었으므로, 
  // Animal 클래스에서의 name은 사용자가 지정한 것에 맞게 진행
    void setName(String name) {
        this.name = name;
    }
}

// Dog는 Animal을 상속받으므로 Animal의 자식클래스가 된다
class Dog extends Animal {
    void sleep() {
        System.out.println(this.name+" zzz");
    }
}

public class Sample {
    public static void main(String[] args) {
        Dog dog = new Dog();
				// setName은 Animal 클래스의 메소드 이나, Dog에서 상속받아서 사용 가능
        dog.setName("poppy");

        System.out.println(dog.name);  // poppy 출력
        dog.sleep();  // poppy zzz 출력
    }
}
```

1) IS-A 관계

- Dog 클래스는 Animal 클래스를 상속 받음
- 즉, Dog는 Animal의 하위 개념

```java
Animal dog = new Dog(); // 가능. 개는 동물이다
Dog dog = new Animal(); // 불가능. 부모 클래스로 만든 객체는 자식 클래스의 자료형으로 사용 불가

// + 추가
// 자바에서 만드는 모든 객체는 Object 자료형으로 사용 가능
Object animal = new Animal();  // Animal is a Object
Object dog = new Dog();  // Dog is a Object

```

2) 메소드 오버라이딩(Overriding)

- 부모 클래스의 메소드를 자식 클래스가 동일한 형태로 또다시 구현하는 행위 **(메소드 덮어쓰기)**
- Before : “happy zzz”가 출력 될 것임

```java
class Animal {
    String name;

    void setName(String name) {
        this.name = name;
    }
}

class Dog extends Animal {
    void sleep() {
        System.out.println(this.name+" zzz");
    }
}

class HouseDog extends Dog {

}

public class Sample {
    public static void main(String[] args) {
        HouseDog houseDog = new HouseDog();
        houseDog.setName("happy");
        houseDog.sleep();  // happy zzz 출력
    }
}
```

After : “happy zzz in house”가 출력

```java
class Animal {
    String name;

    void setName(String name) {
        this.name = name;
    }
}

class Dog extends Animal {
    void sleep() {
        System.out.println(this.name + " zzz");
    }
}

class HouseDog extends Dog {
	// Dog의 sleep() 메소드를 재 정의
    void sleep() {
        System.out.println(this.name + " zzz in house");
    }
}

public class Sample {
    public static void main(String[] args) {
        HouseDog houseDog = new HouseDog();
        houseDog.setName("happy");
        houseDog.sleep();  // happy zzz in house 출력
    }
}
```

3) 메소드 오버로딩(Overloading)

- 이름은 같으나 입력 항목이 다른 여러 개의 메소드 생성방식을 오버로딩이라 부른다
- ex)

 출력 결과 :

happy zzz in house
happy zzz in house for 3 hours

```java
class Animal {
    String name;

    void setName(String name) {
        this.name = name;
    }
}

class Dog extends Animal {
    void sleep() {
        System.out.println(this.name + " zzz");
    }
}

class HouseDog extends Dog {
    void sleep() {
        System.out.println(this.name + " zzz in house");
    }

    void sleep(int hour) {
        System.out.println(this.name + " zzz in house for " + hour + " hours");
    }
}

public class Sample {
    public static void main(String[] args) {
        HouseDog houseDog = new HouseDog();
        houseDog.setName("happy");
        houseDog.sleep();  // happy zzz in house 출력
        houseDog.sleep(3);  // happy zzz in house for 3 hours 출력
    }
}
```

- 클래스는 다중 상속을 지원하지 않으므로 주의

## 4. 인터페이스(Interface)

1) 정의 
다른 클래스를 작성할 때 기본이 되는 틀을 제공하면서, 다른 클래스 사이의 중간 매개 역할까지 담당하는 일종의 추상 클래스를 의미

2) 특징 

- 인터페이스의 모든 필드는 public static final
- 인터페이스의 모든 메소드는 public abstract
- 모든 인터페이스에 공통으로 적용되므로 생략 가능 (자바 컴파일러가 자동 추가)
- 자신이 직접 인스턴스 생성이 불가하다

```java
접근제어자 interface 인터페이스이름 {

    public static final 타입 상수이름 = 값;

    ...

    public abstract 메소드이름(매개변수목록);

    ...
}
```

- ex) 인터페이스 예제

```java
interface Animal { 
public abstract void cry(); 

}
 
class Cat implements Animal {
		// 메소드 오버라이딩
    public void cry() {
        System.out.println("냐옹냐옹!");
    }
}

class Dog implements Animal {
		// 메소드 오버라이딩
    public void cry() {
        System.out.println("멍멍!");
    }
}

 
public class Polymorphism03 {
    public static void main(String[] args) {

        Cat c = new Cat();
        Dog d = new Dog();

        c.cry(); // 냐옹냐옹!
        d.cry(); // 멍멍!
    }
}
```

3) 다중 상속 예제

```java
interface Animal { public abstract void cry(); }

interface Cat extends Animal { public abstract void cry(); }
interface Dog extends Animal { public abstract void cry(); }

 

class MyPet implements Cat, Dog {
    public void cry() {
        System.out.println("멍멍! 냐옹냐옹!");
    }
}

 
public class Polymorphism05 {
    public static void main(String[] args) {
        MyPet p = new MyPet();
        p.cry(); //멍멍! 냐옹냐옹! 출력
    }
}
```

## 5. 추상클래스(Abstract Class)

1) 추상 메소드

- 자식 클래스에서 반드시 오버라이딩 해야만 사용가능한 메소드

```java
abstract 반환타입 메소드이름();
```

2) 추상 클래스

- 하나 이상의 추상 메소드를 포함하는 클래스를 가리켜 추상 클래스(abstract class)라고 지칭

```java
abstract class 클래스이름 {
    ...
    abstract 반환타입 메소드이름();
    ...
}
```

3) 예제 포맷

```java
abstract class Predator extends Animal {
    abstract String getFood();

    ~~default~~ void printFood() {  // default 를 제거한다.
        System.out.printf("my food is %s\n", getFood());
    }

    static int LEG_COUNT = 4;  // 추상 클래스의 상수는 static 선언이 필요하다.
    static int speed() {
        return LEG_COUNT * 30;
    }
}
```

4) 예제

```java
abstract class Animal { abstract void cry(); }

class Cat extends Animal { 
	void cry() { 
	System.out.println("냐옹냐옹!"); 
	} 
}

class Dog extends Animal { 
	void cry() { 
	System.out.println("멍멍!"); 
	} 
}

 

public class Polymorphism02 {

    public static void main(String[] args) {

        // Animal a = new Animal(); // 추상 클래스는 인스턴스를 생성할 수 없음.
        Cat c = new Cat();
        Dog d = new Dog();

        c.cry();
        d.cry();
    }
}
```