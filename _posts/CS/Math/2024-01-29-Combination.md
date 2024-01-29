---
title: 조합 (Combination)
date: 2024-01-29 00:00:00 +00:00
categories: [CS, Math]
tags:
  [
    CS,
    Math
  ]
---

## 1. 조합

- 서로 다른 n개 중에서 r개를 선택하는 경우의 수 (순서 X, 중복 X)
    - 예시) 서로 다른 4명 중 주번 2병 뽑기

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/c6be8ef2-2422-4d38-a0ed-23562a285bd6/Untitled.png)

## 2. 중복조합

- 서로 다른 n개 중에서 r개를 선택하는 경우의 수 (순서 X, 중복 O)
    - 예시) 후보 2명, 유권자 3명일 때 무기명 투표 방법

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/ef10ed3a-05f7-49e0-aec4-32817d6c200a/Untitled.png)

**(실습 : 조합)**

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/4763bbee-7a4f-4919-b369-e20d993d92bd/Untitled.png)

```java
// Practice
// 1, 2, 3, 4 를 이용하여 세자리 자연수를 만드는 방법 (순서 X, 중복 x)의 각 결과를 출력하시오

import java.util.Arrays;

public class Practice {
    void combination(int[] arr, boolean[] visited, int depth, int n, int r) {

        if (r == 0) {
            for (int i = 0 ; i < n ; i++) {
                if (visited[i]) {
                    System.out.print(arr[i] + " ");
                }
            }
            System.out.println();
            return;
        }

        if (depth == n) {
            return;
        }

        visited[depth] = true;
        combination(arr, visited, depth + 1, n ,r - 1);

        visited[depth] = false;
        combination(arr, visited, depth + 1, n ,r);

    }
    
    public static void main(String[] args) {
//      Test code
        int[] arr = {1, 2, 3, 4};
        boolean[] visited = {false, false, false, false};

        Practice p = new Practice();
        p.combination(arr, visited, 0, 4, 3);
    }
}
```
