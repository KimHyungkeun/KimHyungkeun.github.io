---
layout: default
title: 5. Generic
parent: Java
nav_order: 5
---

## 1. 제네릭(Generic) 이란

- 데이터의 타입(data type)을 일반화한다(generalize)는 것을 의미
- 클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정
- JDK 1.5 이전에는 클래스나 메소드에서 인수나 반환값으로 Object 타입을 사용
- 그러나, JDK 1.5부터는 컴파일시에 미리 타입이 정해지므로, 타입 검사나 타입 변환 같은 작업 생략

## 2. 제네릭의 선언 및 생성

1) 예제

- 아래 예제에서 사용된 “T”를 타입 변수(type variable)라고 하며, 임의의 참조형 타입을 의미
- 꼭 “T”가 아니라 어떠한 문자를 사용해도 상관없으며, 여러 개의 타입 변수는 쉼표(,)로 구분

```java
class MyArray<T> {
    T element;
    void setElement(T element) { this.element = element; }
    T getElement() { return element; }
}
```

2) 예제2

- Java SE 7부터는 인스턴스 생성 시 타입을 추정할 수 있는 경우에는 타입 생략 가능

```java
MyArray<Integer> myArr = new MyArray<>(); // Java SE 7부터 가능함.
```

3) 예제3

```java
import java.util.*;

class LandAnimal { 
	public void crying() { 
		System.out.println("육지동물"); 
	} 
}

class Cat extends LandAnimal { 
	public void crying() { 
		System.out.println("냐옹냐옹"); 
	} 
}

class Dog extends LandAnimal { 
	public void crying() { 
		System.out.println("멍멍"); 
	} 
}

class Sparrow { 
	public void crying() { 
		System.out.println("짹짹"); 
	} 
}

class AnimalList<T> {
    ArrayList<T> al = new ArrayList<T>();

    void add(T animal) { al.add(animal); }
    T get(int index) { return al.get(index); }
    boolean remove(T animal) { return al.remove(animal); }
    int size() { return al.size(); }
}

 

public class Generic01 {
    public static void main(String[] args) {
        AnimalList<LandAnimal> landAnimal = new AnimalList<>(); // Java SE 7부터 생략가능함.

        landAnimal.add(new LandAnimal());
        landAnimal.add(new Cat());
        landAnimal.add(new Dog());
        // landAnimal.add(new Sparrow()); // 오류가 발생함.

 

        for (int i = 0; i < landAnimal.size() ; i++) {
            landAnimal.get(i).crying();
        }
    }
}

// 실행 결과
// 육지동물
// 냐옹냐옹
// 멍멍
```

## 3. 제네릭의 제거 시기

- 자바 코드에서 사용된 제네릭 타입은 컴파일 시 컴파일러에 의해 자동으로 검사되어 타입 변환
- 그리고, 코드 내의 모든 제네릭 타입은 제거되어, 컴파일된 class파일에는 어떠한 제네릭 파일도 미포함
- 이런 이유는 제네릭을 사용하지 않는 코드와의 호환성 유지하기 위함

## 4. 타입 변수의 제한

- 제네릭은 “T”와 같은 타입 변수(type variable)를 사용하여 타입 제한
- 이때, extends 키워드를 사용하면 타입 변수에 특정 타입만을 사용

```java
class AnimalList<T extends LandAnimal> { ... }
```

- 위와 같이 타입 변수에 제한을 걸면, 클래스 내부에서 사용된 모든 타입 변수에 제한 걸림
- **이 때, 클래스가 아닌 인터페이스 구현에도 extends를 써야함** (implements 안됨)

```java
interface WarmBlood { ... }
...
class AnimalList<T extends WarmBlood> { ... } // implements 키워드를 사용해서는 안됨.
```

- 클래스와 인터페이스를 동시에 상속 받고 구현해야 한다면 (&) 기호를 사용

```java
class AnimalList<T extends LandAnimal & WarmBlood> { ... }
```

- 예제

```java
import java.util.*;

class LandAnimal { public void crying() { System.out.println("육지동물"); } }
class Cat extends LandAnimal { public void crying() { System.out.println("냐옹냐옹"); } }
class Dog extends LandAnimal { public void crying() { System.out.println("멍멍"); } }
class Sparrow { public void crying() { System.out.println("짹짹"); } }

 

class AnimalList<T extends LandAnimal> {
    ArrayList<T> al = new ArrayList<T>();

 
    void add(T animal) { al.add(animal); }
    T get(int index) { return al.get(index); }
    boolean remove(T animal) { return al.remove(animal); }
    int size() { return al.size(); }
}

 

public class Generic02 {

    public static void main(String[] args) {
        AnimalList<LandAnimal> landAnimal = new AnimalList<>(); // Java SE 7부터 생략가능함.

        landAnimal.add(new LandAnimal());
        landAnimal.add(new Cat());
        landAnimal.add(new Dog());
        // landAnimal.add(new Sparrow()); // 오류가 발생함.

        for (int i = 0; i < landAnimal.size() ; i++) {
            landAnimal.get(i).crying();
        }
    }
}

// 실행 결과
// 육지동물
// 냐옹냐옹
// 멍멍
```

## 5. 제네릭 메소드 (Generic Method)

- 메소드의 선언부에 타입 변수를 사용한 메소드를 의미
- 타입 변수의 선언은 메소드 선언부에서 반환 타입 바로 앞에 위치

```java
public static <T> void sort( ... ) { ... }
```

- 다음 예제의 제네릭 클래스에서 정의된 타입 변수 T와 제네릭 메소드에서 사용된 타입 변수 T는 전혀 별개의 것임을 주의

```java

class AnimalList<T> {

    ...

    public static <T> void sort(List<T> list, Comparator<? super T> comp) {
        ...
    }
    ...
}
```

## 6. 와일드카드의 사용

- 이름에 제한을 두지 않음을 표현하는 데 사용하는 기호

```java
<?>           // 타입 변수에 모든 타입을 사용할 수 있음.
<? extends T> // T 타입과 T 타입을 상속받는 자손 클래스 타입만을 사용할 수 있음.
<? super T>   // T 타입과 T 타입이 상속받은 조상 클래스 타입만을 사용할 수 있음.
```

- 예제

```java
import java.util.*;

 
class LandAnimal { public void crying() { System.out.println("육지동물"); } }
class Cat extends LandAnimal { public void crying() { System.out.println("냐옹냐옹"); } }
class Dog extends LandAnimal { public void crying() { System.out.println("멍멍"); } }
class Sparrow { public void crying() { System.out.println("짹짹"); } }

 

class AnimalList<T> {

    ArrayList<T> al = new ArrayList<T>();

    public static void cryingAnimalList(AnimalList<? extends LandAnimal> al) {
        LandAnimal la = al.get(0);
        la.crying();
    }

 
    void add(T animal) { al.add(animal); }
    T get(int index) { return al.get(index); }
    boolean remove(T animal) { return al.remove(animal); }
    int size() { return al.size(); }
}

public class Generic03 {

    public static void main(String[] args) {
        AnimalList<Cat> catList = new AnimalList<Cat>();
        catList.add(new Cat());
        AnimalList<Dog> dogList = new AnimalList<Dog>();
        dogList.add(new Dog());

        AnimalList.cryingAnimalList(catList);
        AnimalList.cryingAnimalList(dogList);
    }
}

// 실행결과
// 냐옹냐옹
// 멍멍
```