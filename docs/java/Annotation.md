---
layout: default
title: 4. Annotation
parent: Java
nav_order: 4
---

## 1. 정의

- 자바 소스 코드에 추가하여 사용할 수 있는 메타데이터의 일종
- @ 기호를 앞에 붙여서 사용
- JDK 1.5버전 이상부터 사용가능

## 2. 종류

1) 표준 어노테이션 : 자바가 기본적으로 제공

2) 메타 어노테이션 : 어노테이션을 위한 어노테이션

3) 사용자 정의 어노테이션

## 3. 표준 어노테이션

**1) @Override** : **오버라이딩을 올바르게 했는지 컴파일러가 체크**

```java
class Parent{
	void parentMethod(){}
}

class Child extends Parent{
	@Override
    void pparentmethod(){} // 컴파일 에러! 잘못된 오버라이드 스펠링 틀림
```

**2) @Deprecated** : **앞으로 사용하지 않을 것을 권장하는 필드나 메소드에 붙임**

```java
@Deprecated
public int getDate(){
	return normalize().getDayOfMonth();
}
```

**⇒ 그냥 없애는 건 안되는건가?**

→ 이전에 해당 메소드로 개발을 진행한 프로젝트들이 있을 수 있으므로, 유지만 하고, 권장만 하지 않는다

**3) @SuppressWarnings : 컴파일러의 경고메세지가 나타나지 않도록 함**

```java
@SuppressWarnings("unchecked")
ArrayList list = new ArrayList(); // 제네릭 타입을 지정하지 않음!
list.add(obj); // 경고 발생 !!! 경고 내용 = unchecked
```

- Array 선언 시 제네릭을 통해서 타입에 대한 정보를 미기입
- 그래서 타입을 선언하지 않았다는 “unchecked” 경고 발생
- 그러나, SuppressWarnings(”unchecked”)를 입력해 주었기에, 억제된다
- 다수의 경고가 뜰 시, 확인된 경고는 해당 어노테이션을 붙여서 새 경고를 알아보지 못하는 것을 방지하기 위해 사용

**4) @FunctionalInterface :** **함수형 인터페이스에 붙이면, 컴파일러가 올바르게 작성했는지 체크**

- 함수형 인터페이스의 “하나의 추상 메소드만 가져야 한다” 라는 제약을 확인
- 함수형 인터페이스라는 것을 알려주기도 함

## 4. 메타 어노테이션

**1) @Target : 어노테이션을 정의할 때, 적용대상을 지정하는데 사용**

- 종류
    - **ElementType.PACKAGE** : 패키지 선언
    - **ElementType.TYPE** : 타입 선언
    - **ElementType.ANNOTATION_TYPE** : 어노테이션 타입 선언
    - **ElementType.CONSTRUCTOR** : 생성자 선언
    - **ElementType.FIELD** : 멤버 변수 선언
    - **ElementType.LOCAL_VARIABLE** : 지역 변수 선언
    - **ElementType.METHOD** : 메서드 선언
    - **ElementType.PARAMETER** : 전달인자 선언
    - **ElementType.TYPE_PARAMETER** : 전달인자 타입 선언
    - **ElementType.TYPE_USE** : 타입 선언

```java
@Target({TYPE, FIELD, TYPE_USE})
@Retention(RetentionPolicy.SOURCE)
public @interface MyAnnotation{}

@MyAnnotation // 적용 대상이 Type(클래스, 인터페이스)
class MyClass{
	@MyAnnotation //적용 대상이 FIELD인 경우
    int i;

    @MyAnnotation //적용 대상이 TYPE_USE인 경우
    MyClass mc;
}
```

**2) @Retention : 어노테이션이 유지되는 기간을 지정하는데 사용**

- SOURCE : 컴파일 전까지만 유효
- CLASS : 컴파일러가 클래스를 참조할 때까지 유효
- RUNTIME : 컴파일 이후에도 JVM에 의해 계속 참조가 가능

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override{}
```

**3) @Documented : javadoc으로 작성한 문서에 포함시키려면 해당 어노테이션을 붙임**

**4) @Inherited : 어노테이션을 상속**

```java
// 어노테이션을 자손 클래스에 상속하고자 할 때, @Inherited를 쿹임

@Inherited
@interface SuperAnno{}

@SuperAnno
class Parent{}

// <- 여기에 @SuperAnno 가 붙은 것으로 인식
class Child extends Parent{}
```

**5) @Repeatable : 반복해서 붙일 수 있는 어노테이션을 정의할 때 사용**

```java
@Repeatable(ToDos.class)
@interface ToDo{
	String value();
}

@ToDo("delete test codes.")
@ToDo("override inherited methods")
class MyClass{
	~~
}

@interface ToDos{
	ToDo[] value();
}
```

- MyClass에 “@ToDo” 어노테이션이 여러개가 정의된 것을 볼 수 있다
- “@Repeatable” 어노테이션은 위의 “ToDos”처럼 “컨테이너 어노테이션”도 정의해야 함

## 5. 어노테이션 생성

**1) 생성**

```java

@interface 이름{
	타입 요소 이름(); // 어노테이션의 요소를 선언
	    ...
}
```

```java
// 예제
@interface DateTime{
	String yymmdd();
    String hhmmss();
}

@interface TestInfo{
	int count() default 1;
    String testedBy();
    TestType testType();
    DateTime testDate();
}

@TestInfo{
	testedBy="Kim",
    testTools={"JUnit", "AutoTester"},
    testType=TestType.FIRST,
    testDate=@DateTime(yymmdd="210922", hhmmss="211311")
)// count를 생략했으므로 default인 "count=1"이 적용된다.
public class NewClass{...}
```

**2) 어노테이션 요소 특징**

- 적용시 값을 지정하지 않으면, 사용 될 수 있는 기본값 지정이 가능. **(위의 default)**
- 요소가 하나이고 이름이 value일 때는 요소의 이름 생략 가능

```java
@interface TestInfo{
	String value();
}
@TestInfo("passed") // value="passed"와 동일
class NewClass{...}
```

- 요소의 타입이 Array이면, 괄호 { } 를 사용한다

```java
@interface TestInfo{
	String[] testTools();
}

@TestInfo(testTools={"JUnit", "AutoTester"})
@TestInfo(testTools="JUnit") // 요소가 1개일 때는 {}를 사용하지 않아도 된다.
@TestInfo(testTool={}) // 요소가 없으면 {}를 써넣어야 한다.
```

## 6. 어노테이션 그 외

**1) 모든 어노테이션의 조상**

- Annotation은 모든 어노테이션의 조상이지만 상속은 불가

```java
public interface Annotation{
	boolean equals(Object obj);
    int hashCode();
    String toString();

    Class<? extends Annotation> annotationType();
    }
```

**2) 마커 어노테이션**

- 요소가 하나도 정의되지 않은 어노테이션
- 대표적으로 “@Test”. 해당 어노테이션은 테스트 프로그램에게 테스트 대상임을 알림

## 7. 어노테이션 규칙

- 요소의 타입은 기본형, String, enum, 어노테이션, class만 허용
- 괄호 ( ) 안에 매개변수 선언 불가
- 예외 선언 불가
- 요소의 타입을 매개변수로 정의할 수 없다 (<T>)

```java
@interface AnnoConfigTest{
    int id = 100; // 상수 ok
    String major(int i, int j) //매개변수 x
    String minor() throws Exception; // 예외 x
    ArrayList<T> list(); // 요소의 타입을 매개변수 x
```

참고 : [https://velog.io/@jkijki12/annotation](https://velog.io/@jkijki12/annotation)