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

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/6931fecf-9fd4-4522-9ee0-5d5ce233930b)


## 3. 중복 순열

- 서로 다른 n개 중에 r개를 선택하는 경우의 수 (순서 O, 중복 O)
    - 예시) 서로 다른 4개의 수 중 2개를 뽑는 방법 (중복 허용)
    - 예시) 후보 2명, 유권자 3명일때 기명 투표 방법

![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/e58b61c3-25f6-4cc4-a475-f6ea6f367f46)


## 4. 원 순열

- 원 모양의 테이블에 n개의 원소를 나열하는 경우의 수
    - 예시) 원 모양의 테이블에 3명을 앉히는 경우

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/0cda91e6-1c3f-4a1f-b4cf-da238b68e2e9)


**(실습 : 순열)**

![Untitled 3](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/30a1b4fd-b3d5-4735-b14b-0e43903a4d25)


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
