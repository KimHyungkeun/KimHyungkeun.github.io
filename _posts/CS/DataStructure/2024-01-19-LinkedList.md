---
title: LinkedList (링크드리스트)
date: 2024-01-19 00:00:00 +00:00
categories: [CS, DataStructure]
tags:
  [
    CS,
    DataStructure,
    LinkedList
  ]
---

## 1. 연결 리스트 (Linked List)

- 데이터를 링크로 연결해서 관리하는 자료구조
- 자료의 순서는 정해져 있지만, 메모리상 연속성이 보장되지는 않음

## 2. 연결 리스트의 장점

- 데이터 공간을 미리 할당할 필요 없음
- 즉, 리스트의 길이가 가변적이라 데이터 추가/삭제 용이

## 3. 연결 리스트의 단점

- 연결구조를 위한 별도 데이터 공간 필요
- 연결 정보를 찾는 시간이 필요 (접근 속도가 상대적으로 느림)
- 데이터 추가, 삭제 시 앞뒤 데이터의 연결을 재구성하는 작업 필요

## 4. 연결 리스트 기본 구조

- 노드 (Node)
    - 데이터 저장 단위로, 값과 포인터로 구성

<img width="499" alt="Untitled" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/14c4bff3-44b7-4048-a1fa-b56e1eab2b81">


## 5. 연결 리스트 기본 연산

### 1) 데이터 추가 - 가장 앞에 추가

<img width="441" alt="Untitled 1" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/f5f12974-c2cc-4081-a252-e7bc0dee9467">


### 2) 데이터 추가 - 가장 뒤에 추가

<img width="439" alt="Untitled 2" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/d1548528-ee0c-4a4e-9b9f-484d0a02ac8c">


### 3) 데이터 추가 - 중간에 추가

<img width="435" alt="Untitled 3" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/89d2d4b6-7b59-402f-ae91-bf070c2eb661">


### 4) 데이터 삭제 - 가장 앞을 삭제

<img width="299" alt="Untitled 4" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/c209c540-74b8-4d37-bd9b-e5f514572d8c">


### 5) 데이터 삭제 - 가장 뒤를 삭제

<img width="293" alt="Untitled 5" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/9ee2d29a-dc2b-4509-b59a-d3ad871b57e3">


### 6) 데이터 삭제 - 중간을 삭제

<img width="443" alt="Untitled 6" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/12d79377-3a70-4562-b035-38992bc598b6">

**(실습 : 연결 리스트)**

```java
// 단순 연결 리스트 (simple ver.) 기본 구조 구현

// 노드
class Node {
    int data;
    Node next;

    Node() {

    }

    Node(int data, Node next) {
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
    //  연결 리스트 비어있는지 확인
    public boolean isEmpty() {
        if (this.head == null) {
            return true;
        }
        return false;
    }

    //  연결 리스트의 맨 뒤에 데이터 추가
    public void addData(int data) {
        if (this.head == null) {
            this.head = new Node (data, null);
        } else {
            Node cur = this.head;
            while (cur.next != null) {
                cur = cur.next;
            }
            cur.next = new Node(data, null);
        }
    }

    //  연결 리스트의 맨 뒤의 데이터 삭제
    public void removeData() {
        if (this.isEmpty()) {
            System.out.println("List is Empty");
            return;
        }

        Node cur = this.head;
        Node prev = cur;

        while (cur.next != null) {
            prev = cur;
            cur = cur.next;
        }

        if (cur == this.head) {
            this.head = null;
        } else {
            prev.next = null;
        }
    }

    //  연결 리스트에서 데이터 찾기
    public void findData(int data) {
        if (this.isEmpty()) {
            System.out.println("List is Empty");
            return;
        }

        Node cur = this.head;
        while (cur != null) {
            if (cur.data == data) {
                System.out.println("Data exists!");
                return;
            }
            cur = cur.next;
        }
        System.out.println("Data not found!");
    }

    //  연결 리스트의 모든 데이터 출력
    public void showData() {
        if (this.isEmpty()) {
            System.out.println("List is Empty");
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

public class Main {

    public static void main(String[] args) {

//      Test Code
        LinkedList myList = new LinkedList(new Node(1, null));
        myList.showData();      // 1

        myList.addData(2);
        myList.addData(3);
        myList.addData(4);
        myList.addData(5);
        myList.showData();      // 1 2 3 4 5

        myList.findData(3);     // Data exist!
        myList.findData(100);   // Data not found!

        myList.removeData();
        myList.removeData();
        myList.removeData();
        myList.showData();      // 1 2

        myList.removeData();
        myList.removeData();
        myList.removeData();    // List is empty

    }

}
```
