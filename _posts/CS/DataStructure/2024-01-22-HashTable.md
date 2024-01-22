---
title: HashTable (해시테이블)
date: 2024-01-22 00:00:00 +00:00
categories: [CS, DataStructure]
tags:
  [
    CS,
    DataStructure,
    HashTable
  ]
---

## 1. 해시 테이블 (Hash Table)

- 키 (key), 값 (Value)을 대응시켜 저장하는 데이터 구조
    - 키를 통해 해당 데이터에 빠르게 접근 가능
- 해싱
    - 키를 특정 계산식에 넣어 나온 결과를 사용하여 값에 접근하는 과정

## 2. 해시 테이블 구조

- 키 : 해시 테이블 접근을 위한 입력 값
- 해시 함수 : 키를 해시 값으로 매핑하는 연산
- 해시 값 : 해시 테이블의 인덱스
- 해시 테이블 : 키-값을 연관시켜 저장하는 데이터 구조

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/73aaaefb-b47a-43e4-9614-800f4d6883dc)


## 3. 해시 충돌

- 해시 테이블의 같은 공간에 서로 다른 값을 저장하려는 경우
    - 서로 다른 키의 해시 함수를 통한 해시 값이 동일한 경우
- 해시  충돌 해결 방법으로는 크게 **개방 주소법**과 **분리 연결법** 존재

![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/37f742c1-16e1-4be7-9653-2c86243e2a53)


**(실습 : HashTable)**

```java
// 선형 자료구조 - 해시 테이블

import java.util.Hashtable;
import java.util.Map;

public class Main {
    // 해시 함수
    public static int getHash(int key) {
        return key % 5;
    }

    public static void main(String[] args) {
        // 기본 해시 테이블 사용 방법
        Hashtable<String, Integer> ht = new Hashtable();
        ht.put("key1", 10);
        ht.put("key2", 20);
        ht.put("key3", 30);
        ht.put("key3", 50);

        for (Map.Entry<String, Integer> item : ht.entrySet()) {
            System.out.println(item.getKey() + " - " + item.getValue());
        }

        // 해시 충돌 케이스 (해시 함수 사용)
        Hashtable<Integer, Integer> ht2 = new Hashtable();
        ht2.put(getHash(1), 10);
        ht2.put(getHash(2), 20);
        ht2.put(getHash(3), 30);
        ht2.put(getHash(4), 40);
        ht2.put(getHash(5), 50);

        System.out.println("== 충돌 전 ==");
        for (Map.Entry<Integer, Integer> item : ht2.entrySet()) {
            System.out.println(item.getKey() + " - " + item.getValue());
        }

        System.out.println("== 충돌 후 ==");
        ht2.put(getHash(6), 60);
        for (Map.Entry<Integer, Integer> item : ht2.entrySet()) {
            System.out.println(item.getKey() + " - " + item.getValue());
        }
    }
}
```

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/bb8dc98d-5860-40ed-a83a-5cce270706aa)


## 4. 해시 충돌 해결 방법 (1)

- **개방 주소법 (Open Address)**
    - 충돌 시 ,테이블에서 비어 있는 공간의 hash를 찾아 데이터를 저장
    - hash와 value가 1:1 관계 유지
    - 비어 있는 공간 탐색 방법에 따라 분류
        - 선형 탐사법, 제곱 탐사법, 이중 해싱

**1) 선형 탐사법 (Linear Probing)**

- 빈 공간을 순차적으로 탐사하는 방법
    - 충돌 발생 지점부터 이후의 빈 공간을 순서대로 탐사
- 일차 군집화 문제 발생
    - 반복된 충돌 발생 시 해당 지점 주변에 데이터가 몰리는 경우 발생

```java
// Practice2
// 해시 충돌 해결 - 개방 주소법 (선형 탐사법)

class MyHashTable2 extends MyHashTable {

    MyHashTable2(int size) {
        super(size);
    }

    public void setValue(int key, int data) {
        int idx = this.getHash(key);

        if (this.elemCnt == this.table.length) {
            System.out.println("Hash table is full!");
            return;
        } else if (this.table[idx] == null) {
            this.table[idx] = data;
        } else {
            int newIdx = idx;
            while (true) {
                newIdx = (newIdx + 1) % this.table.length;
                if (this.table[newIdx] == null) {
                    break;
                }
            }
            this.table[newIdx] = data;
        }
        elemCnt++;
    }
}

public class Practice2 {
    public static void main(String[] args) {
        // Test code
        MyHashTable2 ht = new MyHashTable2(5);
        ht.setValue(1, 1);
        ht.setValue(3, 3);
        ht.printHashTable();

        ht.setValue(1, 10);
        ht.printHashTable();

        ht.setValue(1, 20);
        ht.setValue(1, 30);
        ht.setValue(1, 40);
        ht.printHashTable();
    }
}
```

![Untitled 3](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/7aa7eb30-0fad-4e1d-bea1-f0039f92b76e)


**2) 제곱 탐사법 (Quadratic Probing)**

- 빈 공간을 n제곱만큼의 간격을 두고 탐사하는 방법
    - 충돌 발생 지점부터 이후의 빈 공간을 n제곱 간격으로 탐사
- 일차 군집화 문제 일부 보완
- 이차 군집화 문제 발생 가능성

```java
// Practice3
// 해시 충돌 해결 - 개방 주소법 (제곱 탐사법)

class MyHashTable3 extends MyHashTable {

    MyHashTable3(int size) {
        super(size);
    }

    public void setValue(int key, int data) {
        int idx = this.getHash(key);

        if (this.elemCnt == this.table.length) {
            System.out.println("Hash table is full!");
            return;
        } else if (this.table[idx] == null) {
            this.table[idx] = data;
        } else {
            int newIdx = idx;
            int cnt = 0;
            while (true) {
                newIdx = (newIdx + (int)Math.pow(2, cnt))  % this.table.length;
                if (this.table[newIdx] == null) {
                    break;
                }
                cnt++;
            }
            this.table[newIdx] = data;
        }
        this.elemCnt++;
    }
}

public class Practice3 {
    public static void main(String[] args) {
        // Test code
        MyHashTable3 ht = new MyHashTable3(11);
        ht.setValue(1, 10);
        ht.setValue(2, 20);
        ht.setValue(4, 40);
        ht.printHashTable();

        ht.setValue(1, 100);
        ht.printHashTable();
    }
}
```

![Untitled 4](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/79460435-7fe9-4fdd-b69b-e39ba4e1a4ad)


**3) 이중 해싱 (Double Hashing)**

- 해싱 함수를 이중으로 사용
    - 해시 함수 1 : 최초 해시를 구할 때 사용
    - 해시 함수 2 : 충돌 발생 시, 탐사 이동 간격을 구할 때 사용
- 선형 탐사, 제곱 탐사에 비해 데이터가 골고루 분포됨

```java
// Practice4
// 해시 충돌 해결 - 개방 주소법 (이중 해싱)

class MyHashTable4 extends MyHashTable {
    int c;

    MyHashTable4(int size) {
        super(size);
        this.c = this.getHashC(size);
    }

    public int getHashC(int size) {
        int c = 0;

        if (size <= 2) {
            return size;
        }

        for (int i = size - 1 ; i > 2 ; i--) {
            boolean isPrime = true;
            for (int j = 2 ; j < i ; j++) {
                if (i % j == 0) {
                    isPrime = false;
                    break;
                }
            }
            if (isPrime) {
                c = i;
                break;
            }
        }

        return c;
    }

    public int getHash2(int key) {
        int hash = 1 + key % this.c;
        return hash;
    }

    public void setValue(int key, int data) {
        int idx = this.getHash(key);

        if (this.elemCnt == this.table.length) {
            System.out.println("Hash table is full!");
            return;
        } else if (this.table[idx] == null) {
            this.table[idx] = data;
        } else {
            int newIdx = idx;
            int cnt = 1;
            while (true) {
                newIdx = (newIdx + this.getHash2(newIdx) * cnt) % this.table.length;
                if (this.table[newIdx] == null) {
                    break;
                }
                cnt++;
            }
            this.table[newIdx] = data;
        }
        this.elemCnt++;
    }

}
public class Practice4 {
    public static void main(String[] args) {
        // Test code
        MyHashTable4 ht = new MyHashTable4(11);
        ht.setValue(1, 10);
        ht.setValue(2, 20);
        ht.setValue(3, 30);
        ht.printHashTable();

        ht.setValue(1, 100);
        ht.setValue(1, 200);
        ht.setValue(1, 300);
        ht.printHashTable();
    }
}
```

## 5. 해시 충돌 해결 방법 (2)

- 분리 연결법 (Seperate Chaining)
    - 해시 테이블을 연결 리스트로 구성
    - 충돌 발생 시, 테이블 내의 다른 위치를 탐색하는 것이 아닌 연결 리스트를 이용하여 해당 테이블에 데이터를 연결

```java
// Practice5
// 해시 충돌 해결 - 분리 연결법

class Node {
    int key;
    int data;
    Node next;

    Node() {}
    Node(int key, int data, Node next) {
        this.key = key;
        this.data = data;
        this.next = next;
    }
}

class LinkedList {
    Node head;

    LinkedList() {}
    LinkedList(Node node) {
        this.head = node;
    }

    public boolean isEmpty() {
        return this.head == null;
    }

    public void addData(int key, int data) {
        if (this.head == null) {
            this.head = new Node(key, data, null);
        } else {
            Node cur = this.head;
            while (cur.next != null) {
                cur = cur.next;
            }
            cur.next = new Node(key, data, null);
        }
    }

    public void removeData(int data) {
        if (this.isEmpty()) {
            System.out.println("List is empty");
            return;
        }

        Node cur = this.head;
        Node pre = cur;
        while (cur != null) {
            if (cur.key == data) {
                if (cur == this.head) {
                    this.head = cur.next;
                } else {
                    pre.next = cur.next;
                }
                break;
            }

            pre = cur;
            cur = cur.next;
        }
    }

    public Integer findData(int data) {
        if (this.isEmpty()) {
            System.out.println("List is empty");
            return null;
        }

        Node cur = this.head;
        while (cur != null) {
            if (cur.key == data) {
                System.out.println("Data exist!");
                return cur.data;
            }
            cur = cur.next;
        }
        System.out.println("Data not found!");
        return null;
    }

    public void showData() {
        if (this.isEmpty()) {
            System.out.println("List is empty!");
            return;
        }

        Node cur = this.head;
        while (cur != null) {
            System.out.print(cur.data + " ");
            cur = cur.next;
        }
        System.out.println();
    }
}

class MyHashTable5 {
    LinkedList[] table;

    MyHashTable5(int size) {
        this.table = new LinkedList[size];
        for (int i = 0 ; i < this.table.length ; i++) {
            this.table[i] = new LinkedList(null);
        }
    }

    public int getHash(int key) {
        return key % this.table.length;
    }

    public void setValue(int key, int data) {
        int idx = this.getHash(key);

        this.table[idx].addData(key, data);
    }

    public int getValue(int key) {
        int idx = this.getHash(key);
        int data = this.table[idx].findData(key);
        return data;
    }

    public void removeValue(int key) {
        int idx = this.getHash(key);

        this.table[idx].removeData(key);
    }

    public void printHashTable() {
        System.out.println("== Hash Table ==");
        for (int i = 0 ; i < this.table.length ; i++) {
            System.out.print(i + ": ");
            this.table[i].showData();
        }
    }

}

public class Practice5 {
    public static void main(String[] args) {
        // Test code
        MyHashTable5 ht = new MyHashTable5(11);
        ht.setValue(1, 10);
        ht.setValue(2, 20);
        ht.setValue(3, 30);
        ht.printHashTable();
        ht.setValue(12, 11);
        ht.setValue(23, 12);
        ht.setValue(34, 13);

        ht.setValue(13, 21);
        ht.setValue(24, 22);
        ht.setValue(35, 23);

        ht.setValue(5, 1);
        ht.setValue(16, 2);
        ht.setValue(27, 3);
        ht.printHashTable();

    }
}
```

![Untitled 5](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/04f368cf-7ff4-445f-96ff-5033ea83cf97)

