---
title: 컬렉션 프레임워크(Collection Framework)
date: 2024-01-08 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

## 1. 컬렉션 프레임워크(Collection Framework)

- 여러 데이터를 편리하게 관리할 수 있게 만들어 놓은 것
    - 자료구조 및 알고리즘을 구조화
- 대표 인터페이스
    - 리스트 인터페이스, Set 인터페이스 Map 인터페이스
    
    ![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/a006cc1a-b16e-4d9f-ad93-c20eb4450846)

    

## 2. List 인터페이스

- 순서가 있는 데이터의 집합
- 데이터 중복 허용
- 대표 구현 클래스
    - ArrayList
    - LinkedList
    - Vector

```java
ArrayList list1 = new ArrayList();
LinkedList list2 = new LinkedList();
Vector v = new Vector();
```

**(실습1 : List)**

- 추가 ArrayList와 LinkedList의 차이
    - ArrayLists는 삭제 및 추가 시, 인덱스를 활용하여 추가 및 삭제가 가능
    - 반면 LinkedList는 인덱스 사용 불가

```java
// Java 프로그래밍 - 컬렉션 프레임워크

import java.util.*;

public class Main {

    public static void main(String[] args) {

//      1. List
//        1-1. ArrayList
        ArrayList list1 = new ArrayList();
        list1.add(1);
        list1.add(2);
        list1.add(3);
        System.out.println("list1 = " + list1);
        list1.remove(Integer.valueOf(2));
        System.out.println("list1 = " + list1);
        list1.add(0, 10); //인덱스를 이용한 값 추
        System.out.println("list1 = " + list1);
        System.out.println("list1.size() = " + list1.size());
        System.out.println("list1.contains(1) = " + list1.contains(1));
        System.out.println("list1.indexOf(10) = " + list1.indexOf(10));

//      1-2. LinkedList
        System.out.println("== LinkedList ==");
        LinkedList list2 = new LinkedList();
        list2.add(1);
        list2.add(2);
        list2.add(3);
        System.out.println("list2 = " + list2);
        list2.addFirst(10);
        list2.addLast(20);
        System.out.println("list2 = " + list2);
        list2.remove(Integer.valueOf(1));
        System.out.println("list2 = " + list2);
        list2.removeFirst();
        list2.removeLast();
        System.out.println("list2 = " + list2);
        System.out.println("list2.size() = " + list2.size());

    }
}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/7574c7d4-9a44-429a-abac-5fa4c3cc4305)


## 3. Set 인터페이스

- 순서가 없는 데이터의 집합
- 데이터의 중복 허용이 되지 않음
- 대표 구현 클래스
    - HashSet
    - TreeSet

```java
HashSet set1 = new HashSet();
TreeSet set2 = new TreeSet();
```

**(실습2 : Set)**

```java
// Java 프로그래밍 - 컬렉션 프레임워크

import java.util.*;

public class Main {

    public static void main(String[] args) {

//      2. Set
//      2-1. HashSet
        System.out.println("== HashSet ==");
        HashSet set1 = new HashSet();
        set1.add(1);
        set1.add(2);
        set1.add(3);
        System.out.println("set1 = " + set1);
        set1.remove(1);
        System.out.println("set1 = " + set1);
        set1.add(2);
        set1.add(3);
        System.out.println("set1 = " + set1);
        System.out.println("set1.size() = " + set1.size());
        System.out.println("set1.contains(2) = " + set1.contains(2));

//      2-2. TreeSet
        System.out.println("== TreeSet ==");
        TreeSet set2 = new TreeSet();
        set2.add(1);
        set2.add(2);
        set2.add(3);
        System.out.println("set2 = " + set2);
        set2.remove(2);
        System.out.println("set2 = " + set2);
        set2.clear();
        System.out.println("set2 = " + set2);
        set2.add(10);
        set2.add(5);
        set2.add(15);
        set2.add(15);
        System.out.println("set2 = " + set2);
        System.out.println("set2.first() = " + set2.first());
        System.out.println("set2.last() = " + set2.last());
        System.out.println("set2.lower(10) = " + set2.lower(10));
        System.out.println("set2.higher(10) = " + set2.higher(10));

    }
}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/8643b997-8ff7-4459-8cd4-3c9211507fc7)


## 4. Map 인터페이스

- 키와 값의 쌍으로 이루어진 데이터 집합
- 순서를 유지 하지 않음
- 대표 구현 클래스
    - HashMap
    - TreeMap

```java
HashMap map1 = new HashMap();
TreeMap map2 = new TreeMap();
```

**(실습3:Map)**

```java
// Java 프로그래밍 - 컬렉션 프레임워크

import java.util.*;

public class Main {

    public static void main(String[] args) {

//      3. Map
//      3-1. HashMap
        System.out.println("== HashMap ==");
        HashMap map1 = new HashMap();
        map1.put(1, "kiwi");
        map1.put(2, "apple");
        map1.put(3, "mango");
        System.out.println("map1 = " + map1);

        map1.remove(2);
        System.out.println("map1 = " + map1);
        System.out.println("map1.get(1) = " + map1.get(1));

//      3-2. TreeMap
        System.out.println("== TreeMap ==");
        TreeMap map2 = new TreeMap();
        map2.put(10, "kiwi");
        map2.put(5, "apple");
        map2.put(15, "mango");
        System.out.println("map2 = " + map2);

        System.out.println("map2.firstEntry() = " + map2.firstEntry());
        System.out.println("map2.firstKey() = " + map2.firstKey());
        System.out.println("map2.lastEntry() = " + map2.lastEntry());
        System.out.println("map2.lastKey() = " + map2.lastKey());
        System.out.println("map2.lowerEntry(10) = " + map2.lowerEntry(10));
        System.out.println("map2.higherEntry(10) = " + map2.higherEntry(10));

    }
}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/1a12c206-94d4-4036-b5a2-24b8348ff024)
