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

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/43d9c5a0-5733-40aa-9f0e-d5f18a263d58)


## 2. 중복조합

- 서로 다른 n개 중에서 r개를 선택하는 경우의 수 (순서 X, 중복 O)
    - 예시) 후보 2명, 유권자 3명일 때 무기명 투표 방법

![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/0008813b-ea4e-4518-ac02-70f72d7a74da)


**(실습 : 조합)**

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/fbfc7adb-73ce-4d54-88c2-35b98cc51925)


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
