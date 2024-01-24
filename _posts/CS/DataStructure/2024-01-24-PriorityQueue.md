---
title: PriorityQueue (우선순위 큐)
date: 2024-01-15 00:00:00 +00:00
categories: [CS, DataStructure]
tags:
  [
    CS,
    DataStructure,
    PriorityQueue
  ]
---

## 1. 우선순위 큐

- 우선순위가 높은 데이터가 먼저 나옴 (≠ FIFO)
    - 모든 데이터에 우선순위가 높음
    - Dequeue 시, 우선순위가 높은 순으로 나감
    - 우선순위가 같은 경우는 FIFO

## 2. 우선순위 큐 - Enqueue, Dequeue

<img width="487" alt="Untitled" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/120ce20f-4734-4667-982e-9443a912d3dd">


## 3. 우선순위 큐 - 구현

<img width="414" alt="Untitled 1" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/0902416b-adf2-4eab-8976-a7184062fb4c">


**(예제 : 우선순위 큐)**

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/6d3114bd-7557-49df-9d8e-477e471a933d)


```java
// 비선형 자료구조 - 우선순위 큐
// 연결 리스트를 이용한 우선순위 큐
// 자바 기본 PriorityQueue

import java.util.*;

public class Main {
    public static void enqueue(LinkedList<Integer> list, int data) {
        int idx = list.size();
        for (int i = 0 ; i < list.size() ; i++) {
            if (list.get(i) > data) {
                idx = i;
                break;
            }
        }
        list.add(idx, data);
    }

    public static Integer dequeue(LinkedList<Integer> list) {
        if (list.size() == 0) {
            return null;
        }

        int data = list.get(0);
        list.remove(0);
        return data;
    }

    public static void main(String[] args) {
        // 연결리스트를 이용한 우선순위 큐
        System.out.println("== 연결리스트 방식의 우선순위 큐 ==");
        LinkedList<Integer> pqList = new LinkedList();
        enqueue(pqList, 5);
        enqueue(pqList, 7);
        enqueue(pqList, 3);
        enqueue(pqList, 1);
        enqueue(pqList, 9);
        System.out.println(pqList);

        System.out.println(dequeue(pqList));
        System.out.println(dequeue(pqList));
        System.out.println(pqList);
        System.out.println();

        // 자바 기본 PriorityQueue 사용
        System.out.println("== 자바 기본 우선순위 큐 ==");
        // 우선순위: 낮은 숫자 순
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        pq.add(5);
        pq.add(7);
        pq.add(3);
        pq.add(1);
        pq.add(9);
        System.out.println(Arrays.toString(pq.toArray()));

        // 우선순위: 높은 숫자 순
        System.out.println("== 자바 기본 우선순위 큐 내림차순==");
        PriorityQueue<Integer> pq2 = new PriorityQueue<>(Collections.reverseOrder());
        pq2.add(5);
        pq2.add(7);
        pq2.add(3);
        pq2.add(1);
        pq2.add(9);
        System.out.println(Arrays.toString(pq2.toArray()));

    }
}
```
