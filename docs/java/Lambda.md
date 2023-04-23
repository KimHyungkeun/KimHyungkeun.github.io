---
layout: default
title: 6. Lambda
parent: Java
nav_order: 6
---

## 1. 람다함수

 1) 정의 

- 프로그래밍 언어에서 사용되는 개념으로 익명 함수를 지칭
- 익명함수 : 이름이 없는 함수
- 익명함수는 일급객체(First Class citizen)이라는 특징을 가짐
- 일급 객체란 일반적으로 다른 객체들에 적용 가능한 연산을 모두 지원하는 개체

2) 람다의 표현식

```java
//정상적인 유형
() -> {}
() -> 1
() -> { return 1; }

(int x) -> x+1
(x) -> x+1
x -> x+1
(int x) -> { return x+1; }
x -> { return x+1; }

(int x, int y) -> x+y
(x, y) -> x+y
(x, y) -> { return x+y; }

(String lam) -> lam.length()
lam -> lam.length()
(Thread lamT) -> { lamT.start(); }
lamT -> { lamT.start(); }

//잘못된 유형 : 선언된 type과 선언되지 않은 type을 같이 사용 할 수 없다.
(x, int y) -> x+y
(x, final y) -> x+y
```

3) 람다식 예제

- 기존 자바 문법

```java
List<String> list = Arrays.asList(new String[]{"a", "b", "c"});
for(int i = 0 ;  i < list.size() ; i++ ) {	
	System.out.println(list.get(i));
}

```

- 람다식 문법

```java
list.forEach(x -> System.out.println(x));
```

## 2. 함수형 인터페이스 (JDK 8부터 가능)

1) 정의

- Functional Interface는 **오직 하나의 메소드 선언을 갖는 인터페이스**를 말함
- 자바 컴파일러는 함수형 인터페이스에 두 개 이상의 메소드가 선언되면 오류 발생

```java
@FunctionalInterface
interface MyFunction {		
	void methodA(); // 오직하나의 메소드 선언		
	
	// 이런건 메소드 구현 까지 포함	
	default void methodB(){ 		
	System.out.println("Call Method B");	
	}
}
```

- 이 때, MyFunction 오브젝트를 파라미터로 받는 다음과 같은 method가 있다면, 
구현체를 생성하여 호출

```java
public static void test(MyFunction f) {	
	f.methodA();
}

MyFunction f = new MyFunction() {	
	@Override	
	public void methodA() {		
		System.out.println("Call Method A");	
	}
};
test(f);

```

- Lambda 표현식으로 호출

```java
test(()->{System.out.println("Call Method A");});
```

2) 함수형 인터페이스 람다 사용예제

- 함수형 Interface 선언

```java
@FunctionalInterface
interface Math {
    public int Calc(int first, int second);
}
```

- 추상 메소드 구현 및 함수형 인터페이스 사용

```java
public static void main(String[] args){

   Math plusLambda = (first, second) -> first + second;
   System.out.println(plusLambda.Calc(4, 2));

   Math minusLambda = (first, second) -> first - second;
   System.out.println(minusLambda.Calc(4, 2));

}

// 실행결과
// 6
// 2
```

출처 : [https://javaplant.tistory.com/32?category=787876](https://javaplant.tistory.com/32?category=787876)

출처 : [https://khj93.tistory.com/entry/JAVA-람다식Rambda란-무엇이고-사용법](https://khj93.tistory.com/entry/JAVA-%EB%9E%8C%EB%8B%A4%EC%8B%9DRambda%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%82%AC%EC%9A%A9%EB%B2%95)