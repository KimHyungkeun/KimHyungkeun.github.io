---
layout: default
title: 3. Reflection
parent: Java
nav_order: 3
---

## 1. 정의

- **구체적인 클래스 타입을 알지 못해도 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API**
- **런타임에 지금 실행되고 있는 클래스를 가져와서 실행해야 하는 경우**
- **동적으로 객체를 생성하고 메소드를 호출하는 방법**

## 2. **어떤 경우에 사용되나?**

- 코드를 작성할 시점에는 어떤 타입의 클래스를 사용할지 모르지만, **런타임 시점에 지금 실행되고 있는 클래스**를 가져와서 실행해야 하는 경우

## 3. 예제

```java
class Person {
    int age;

    Person() {
        this.age = 27;
    }

    Person(int age) {
        this.age = age;
    }

    int getAge() {
        return this.age;
    }
}
```

1) 생성자 찾기

```java
Class clazz = Class.forName("Person");
Constructor constructor = clazz.getDeclaredConstructor();

//getDeclaredConstructor()는 인자가 없는 생성자를 가져온다.
```

2) Method 찾기

```java
Class clazz = Person.class;
Method[] methodList = clazz.getDeclaredMethods();
System.out.println(methods[0].invoke(clazz.newInstance())) // 27이 출력됨
```

3) Field 변경

```java
Class clazz = Person.class;
Field[] field = clazz.getDeclaredFields();

Person person = new Person();
field[0].set(person, 17);
System.out.println(field[0].get(person));  // 17이 출력됨
```

## 4. 예제2

1) 아래와 같이 Car class를 생성

```java
public class Car {
    private final String name;
    private int position;

    public Car(String name, int position) {
        this.name = name;
        this.position = position;
    }

    public void move() {
        this.position++;
    }

    public int getPosition() {
        return position;
    }
}
```

2) obj 객체를 생성 

- obj는 컴파일 타임에 Object 타입으로 결정이 났으므로, Object 클래스의 인스턴스 변수 및 메소드만 사용 가능하게 됨

- (Before) 하단과 같이 에러 발생

```java
public static void main(String[] args) {
    Object obj = new Car("foo", 0);
    obj.move();    // 컴파일 에러 발생 java: cannot find symbol
}
```

- (After) Reflection을 이용한 Car 메소드 호출

```java
public static void main(String[] args) throws Exception {
    Object obj = new Car("foo", 0);
    Class carClass = Car.class;
    Method move = carClass.getMethod("move");

    // move 메서드 실행, invoke(메서드를 실행시킬 객체, 해당 메서드에 넘길 인자)
    move.invoke(obj, null);

    Method getPosition = carClass.getMethod("getPosition");
    int position = (int)getPosition.invoke(obj, null);
    System.out.println(position);
    // 출력 결과: 1
}
```