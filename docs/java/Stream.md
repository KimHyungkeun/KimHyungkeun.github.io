---
layout: default
title: 7. Lambda
parent: Java
nav_order: 7
---

## 1. Stream API란

- JDK8부터 지원하는 함수형 프로그래밍 API
- 데이터를 추상화하고, 처리하는데 자주 사용되는 함수들

Before)

```java
// Stream 사용 전
String[] nameArr = {"IronMan", "Captain", "Hulk", "Thor"}
List<String> nameList = Arrays.asList(nameArr);

// 원본의 데이터가 직접 정렬됨
Arrays.sort(nameArr);
Collections.sort(nameList);

for (String str: nameArr) {
  System.out.println(str);
}

for (String str : nameList) {
  System.out.println(str);
}
```

After)

```java
// Stream 사용 후
String[] nameArr = {"IronMan", "Captain", "Hulk", "Thor"}
List<String> nameList = Arrays.asList(nameArr);

// 원본의 데이터가 아닌 별도의 Stream을 생성함
Stream<String> nameStream = nameList.stream();
Stream<String> arrayStream = Arrays.stream(nameArr);

// 복사된 데이터를 정렬하여 출력함
nameStream.sorted().forEach(System.out::println);
arrayStream.sorted().forEach(System.out::println);
```

특징)

1) 원본의 데이터를 변경하지 않음 

- Stream API는 원본 데이터를 조회하여 원본의 데이터가 아닌 별도의 요소들로 Stream 생성

```java
List<String> sortedList = nameStream.sorted()
							.collect(Collections.toList());
```

2) Stream은 일회용

- 한번 사용이 끝나면 재사용이 불가능 하므로 Stream이 필요하면 Stream을 재생성

```java
userStream.sorted().forEach(System.out::print);

// 스트림이 이미 사용되어 닫혔으므로 에러 발생
int count = userStream.count(); 

// IllegalStateException 발생
java.lang.IllegalStateException: stream has already been operated upon or closed
    at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:229)
    at java.util.stream.ReferencePipeline.noneMatch(ReferencePipeline.java:459)
```

3) 내부 반복으로 작업을 처리

- 기존에는 반복문을 위해 for나 while을 사용하나, stream은 반복 문법을 메소드 내부에 숨김

```java
// 반복문이 forEach라는 함수 내부에 숨겨져 있다.
nameStream.forEach(System.out::println);
```

## 2. Stream API 연산 종류

**1) 생성하기**

- Stream 객체를 생성
- Stream 객체는 재사용이 불가하므로, 닫히면 재생성 필요

**2) 가공하기**

- 원본의 데이터를 별도의 데이터로 가공하기 위한 중간 연산
- 연산 결과를 Stream으로 반환하기 때문에, 연속해서 중간연산 가능

**3) 결과 만들기**

- 가공된 데이터로부터 원하는 결과를 만들기 위한 최종 연산
- Stream의 요소들을 소모하면서 연산이 수행되므로, 1번만 처리가능

**ex)**

```java
List<String> myList = Arrays.asList("a1", "a2", "b1", "c2", "c1");

myList
    .stream()							// 생성하기
    .filter(s -> s.startsWith("c"))			// 가공하기
    .map(String::toUpperCase)			// 가공하기
    .sorted()							// 가공하기
    .count();							// 결과만들기

```

## 3. Stream 생성하기

**1) Collection의 Stream 생성**

```java
// List로부터 스트림을 생성
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> listStream = list.stream();
```

**2) 배열의 Stream 생성**

```java
// 배열로부터 스트림을 생성
Stream<String> stream = Stream.of("a", "b", "c"); //가변인자
Stream<String> stream = Stream.of(new String[] {"a", "b", "c"});
Stream<String> stream = Arrays.stream(new String[] {"a", "b", "c"});
Stream<String> stream = Arrays.stream(new String[] {"a", "b", "c"}, 0, 3); //end범위 포함 x
```

**3) 원시 Stream 생성**

```java
// 4이상 10 이하의 숫자를 갖는 IntStream
IntStream stream = IntStream.range(4, 10);
```

## 4. Stream 가공하기(중간연산)

**1) 필터링 - filter**

```java
// stream에 "a"가 포함되는 것들만 추려냄
Stream<String> stream = 
  list.stream()
  .filter(name -> name.contains("a"));
```

**2) 데이터 변환 - Map**

```java
// stream을 모두 대문자로 바꾸고 싶을때
Stream<String> stream = 
  names.stream()
  .map(s -> s.toUpperCase());
```

```java
Stream<File> fileStream = Stream.of(new File("Test1.java"), new File("Test2.java"), new File("Test3.java"));

//Stream<File> --> Stream<String> 변환
Stream<String> fileNameStream = fileStream.map(File::getName);
```

**3) 정렬 - Sorted**

```java
List<String> list = Arrays.asList("Java", "Scala", "Groovy", "Python", "Go", "Swift");

Stream<String> stream = list.stream()
  .sorted()
// [Go, Groovy, Java, Python, Scala, Swift] : 오름차순 정렬

Stream<String> stream = list.stream()
  .sorted(Comparator.reverseOrder())
// [Swift, Scala, Python, Java, Groovy, Go] : 내림차순 정렬

```

**4) 중복 제거 - Distinct**

```java
List<String> list = Arrays.asList("Java", "Scala", "Groovy", "Python", "Go", "Swift", "Java");

Stream<String> stream = list.stream()
  .distinct()
// [Java, Scala, Groovy, Python, Go, Swift]
```

**5) 특정 연산 - peek**

```java
// Stream에 영향을 주지 않고 특정 연산을 수행하기 위한 함수
// 아래와 같이 중간에 요소들의 값을 출력하고 싶을때 println을 사용한다
int sum = IntStream.of(1, 3, 5, 7, 9)
  .peek(System.out::println)
  .sum();
```

**6) 원시 Stream ↔ Stream**

```java
// IntStream -> Stream<Integer>
IntStream.range(1, 4)
    .mapToObj(i -> "a" + i)

// Stream<Double> -> IntStream -> Stream<String>
Stream.of(1.0, 2.0, 3.0)
    .mapToInt(Double::intValue)
    .mapToObj(i -> "a" + i)
```

## 5. Stream 결과 만들기 (최종 연산)

**1) 최대/최소/총합/평균/갯수 - Max/Min/Sum/Average/Count**

```java
// min이나 max 또는 average는 Stream이 비어있으면 특정 불가
OptionalInt min = IntStream.of(1, 3, 5, 7, 9).min();
int max = IntStream.of().max().orElse(0);
IntStream.of(1, 3, 5, 7, 9).average().ifPresent(System.out::println);
```

**2) 데이터 수집 - collect**

```java
collect() : 스트림의 최종연산, 매개변수로 Collector를 필요로 한다.
Collector : 인터페이스, collect의 파라미터는 이 인터페이스를 구현해야한다.
Collectors : 클래스, static메소드로 미리 작성된 컬렉터를 제공한다.

// collect의 파라미터로 Collector의 구현체가 와야 한다.
Object collect(Collector collector)
```

**ex)**

```java
List<Product> productList = Arrays.asList(
	new Product(23, "potatoes"),
	new Product(14, "orange"),
	new Product(13, "lemon"),
	new Product(23, "bread"),
	new Product(13, "sugar"));
```

- **Collectors.toList()**
- Stream에서 작업한 결과를 List로 반환받을 수 있다.

```java
List<String> nameList = productList.stream()
    .map(Product::getName)
    .collect(Collectors.toList());
```

- **Collectors.joining()**
- Collectors.joining()에 들어가는 인자 종류
    - delimiter : 각 요소 중간에 들어가 요소를 구분시켜주는 구분자
    - prefix : 결과 맨 앞에 붙는 문자
    - suffix : 결과 맨 뒤에 붙는 문자

```java

String listToString = productList.stream()
	.map(Product::getName)
	.collect(Collectors.joining());
// potatoesorangelemonbreadsugar

String listToString = productList.stream()
	.map(Product::getName)
	.collect(Collectors.joining(" "));
// potatoes orange lemon bread sugar

String listToString = productList.stream()
  	.map(Product::getName)
  	.collect(Collectors.joining(", ", "<", ">"));
// <potatoes, orange, lemon, bread, sugar>

```

- Collectors.averageingInt(), Collectors.summingInt(), Collectors.summarizingInt()

```java
Double averageAmount = productList.stream()
	.collect(Collectors.averagingInt(Product::getAmount));

// 86
Integer summingAmount = productList.stream()
	.collect(Collectors.summingInt(Product::getAmount));

// 86
Integer summingAmount = productList.stream()
    .mapToInt(Product::getAmount)
    .sum();

//IntSummaryStatistics {count=5, sum=86, min=13, average=17.200000, max=23}
IntSummaryStatistics statistics = productList.stream()
    .collect(Collectors.summarizingInt(Product::getAmount));

```

- **Collectors.groupingBy()**
- Stream에서 작업한 결과를 특정 그룹으로 묶기를 원할 수 있다.

```java
Map<Integer, List<Product>> collectorMapOfLists = productList.stream()
  .collect(Collectors.groupingBy(Product::getAmount));

/*
{23=[Product{amount=23, name='potatoes'}, Product{amount=23, name='bread'}], 
 13=[Product{amount=13, name='lemon'}, Product{amount=13, name='sugar'}], 
 14=[Product{amount=14, name='orange'}]}
 */
```

- **Collectors.partitioningBy()**
- 함수형 인터페이스 Predicate를 받아 Boolean을 Key값으로 partitioning한다.

```java
Map<Boolean, List<Product>> mapPartitioned = productList.stream()
	.collect(Collectors.partitioningBy(p -> p.getAmount() > 15));

/*
{false=[Product{amount=14, name='orange'}, Product{amount=13, name='lemon'}, Product{amount=13, name='sugar'}], 
 true=[Product{amount=23, name='potatoes'}, Product{amount=23, name='bread'}]}
 */
```

**3) 조건 검사 - Match**

- 조건검사의 match 함수의 종류
    - anyMatch: 1개의 요소라도 해당 조건을 만족하는가
    - allMatch: 모든 요소가 해당 조건을 만족하는가
    - nonMatch: 모든 요소가 해당 조건을 만족하지 않는가

```java
List<String> names = Arrays.asList("Eric", "Elena", "Java");

boolean anyMatch = names.stream()
    .anyMatch(name -> name.contains("a"));
boolean allMatch = names.stream()
    .allMatch(name -> name.length() > 3);
boolean noneMatch = names.stream()
    .noneMatch(name -> name.endsWith("s"));
```

**4) 특정 연산 수행 - forEach**

- forEach()는 최종 연산으로써 실제 요소들에 영향을 줄 수 있으며, 반환값이 존재하지 않는다.
- 예를 들어 요소들을 출력하기를 원할 때 다음과 같이 forEach를 사용할 수 있다.

```java
names.stream()
    .forEach(System.out::println);
```

출처: https://mangkyu.tistory.com/114 [MangKyu's Diary:티스토리]