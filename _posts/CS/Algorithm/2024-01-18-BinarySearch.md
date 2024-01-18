---
title: 이진 탐색 (Binary Search)
date: 2014-01-18 HH:MM:SS +00:00
categories: [CS, Algorithm]
tags:
  [
    CS,
    Algorithm,
    BinarySearch
  ]
---

## 1. 이진 탐색

- 정렬된 상태의 데이터에서 특정 값을 빠르게 탐색하는 방법
    - 찾고자 하는 값과 데이터 중앙에 있는 값을 비교
    - 찾고자 하는 값이 더 작으면 데이터 왼쪽 부분에서 이진 탐색
    - 찾고하 하는 값이 더 크면 데이터 오른쪽 부분에서 이진 탐색
- 알고리즘 시간 복잡도 : O(logN)

## 2. 이진 탐색 과정

- 데이터가 우선 정렬된 상태여야 이진 탐색 진행 가능
- 찾고자하는 데이터 : 30

<img width="411" alt="Untitled" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/fedeea41-2830-43ca-8303-f78e805060b7">


**(실습 1 : 반복문, 재귀함수를 이용한 이진탐색)**

```java
// 알고리즘 - 이진 탐색

public class Main {
    // 반복문 구조
    public static int binarySearch(int arr[], int target) {
        int left = 0;
        int right = arr.length - 1;

        while (left <= right) {
            int mid = (left + right) / 2;
            if (target == arr[mid]) {
                return mid;
            } else if (target < arr[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }

        return -1;
    }
    
    // 재귀 호출 구조
    public static int binarySearch2(int[] arr, int target, int left, int right) {

        while (left <= right) {
            int mid = (left + right) / 2;
            if (target == arr[mid]) {
                return mid;
            } else if (target < arr[mid]) {
                right = mid - 1;
                return binarySearch2(arr, target, left, right);
            } else {
                left = mid + 1;
                return binarySearch2(arr, target, left, right);
            }
        }

        return -1;
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 5, 10, 20, 30, 40, 50, 60};

        System.out.println("Index: " + binarySearch(arr, 30));
        System.out.println();

        System.out.println("Index: " + binarySearch2(arr, 30, 0, arr.length - 1));
    }
}
```

<img width="62" alt="Untitled 1" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/f6b54486-af97-4c18-a2f5-c1f70328aa09">


**(실습 2 : Java binarySearch 메소드 사용)**

```java
// 자바 기본 binarySearch

import java.util.Arrays;

public class Main2 {
    public static void main(String[] args) {
        int[] arr = {1, 2, 5, 10, 20, 30, 40, 50, 60};

        System.out.println("== 데이터가 있는 경우 ==");
        System.out.println(Arrays.binarySearch(arr, 1));
        System.out.println(Arrays.binarySearch(arr, 10));
        System.out.println(Arrays.binarySearch(arr, 30));

        System.out.println("== 데이터가 없는 경우 ==");
        System.out.println(Arrays.binarySearch(arr, 3));
        System.out.println(Arrays.binarySearch(arr, 11));
        System.out.println(Arrays.binarySearch(arr, 35));
    }
}
```

<img width="142" alt="Untitled 2" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/ff5949ad-4fd4-4c96-9592-bfd0cdad4206">


- 설명 (데이터가 없는 경우)
    
    ```java
    arr = {1, 2, 5, 10, 20, 30, 40, 50, 60};
    System.out.println(Arrays.binarySearch(arr, 3));
    ```
    
    - **3**은 **2(idx로는 1번째)** 다음에 와야하는데 그렇지 않으므로, 본래 삽입되어야할 idx인 2를 -2로 바꾼 후, -1을 더하여 -3이 된다
