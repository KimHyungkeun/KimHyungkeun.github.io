---
title: 순열 (Permutation)
date: 2024-01-29 00:00:00 +00:00
categories: [CS, Math]
tags:
  [
    CS,
    Math
  ]
---

## 1. 팩토리얼

```
1! = 1
2! = 1 x 2
3! = 1 x 2 x 3

n! = n(n-1)(n-2)(n-3)...
```

## 2. 순열 (Permutation)

- 순서를 정해서 나열
- 서로 다른 n개 중에 r개를 선택하는 경우의 수 (순서O, 중복X)
    - 예시1) 5명을 3줄로 세우는 방법
    - 예시2) 서로 다른 4명 중 반장, 부반장 뽑는 방법

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/b32e02af-0192-45bc-94f1-df2eb1750ace/Untitled.png)

## 3. 중복 순열

- 서로 다른 n개 중에 r개를 선택하는 경우의 수 (순서 O, 중복 O)
    - 예시) 서로 다른 4개의 수 중 2개를 뽑는 방법 (중복 허용)
    - 예시) 후보 2명, 유권자 3명일때 기명 투표 방법

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/5b321ab4-2843-4ad7-bfcb-522283e811be/Untitled.png)

## 4. 원 순열

- 원 모양의 테이블에 n개의 원소를 나열하는 경우의 수
    - 예시) 원 모양의 테이블에 3명을 앉히는 경우

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/2659e0f1-4f8e-4a8f-83f7-91173b5d9574/Untitled.png)

**(실습 : 순열)**

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/1a004728-a410-4d11-b322-87c525d98b11/Untitled.png)

```java
// Practice2
// 1, 2, 3, 4 를 이용하여 세자리 자연수를 만드는 방법 (순서 O, 중복 x)의 각 결과를 출력하시오
// 방법 2

import java.util.Arrays;

public class Practice2 {
    void permutation(int[] arr, int depth, int n, int r, boolean[] visited, int[] out) {
        if (depth == r) {
            System.out.println(Arrays.toString(out));
            return;
        }

        for (int i = 0 ; i < n ; i++) {
            if (visited[i] != true) {
                visited[i] = true;
                out[depth] = arr[i];
                permutation(arr, depth + 1, n, r, visited, out);
                visited[i] = false;
            }
        }
    }

    public static void main(String[] args) {
//      Test code
        int n = 4;
        int r = 3;
        int[] arr = {1, 2, 3, 4};
        boolean[] visited = new boolean[n];
        int[] out = new int[r];

        Practice2 p = new Practice2();
        p.permutation(arr, 0, n, r, visited, out);
    }
}
```