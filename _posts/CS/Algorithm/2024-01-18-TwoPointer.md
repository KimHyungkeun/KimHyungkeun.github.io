---
title: 투포인터 (TwoPointer)
date: 2024-01-18 00:00:00 +00:00
categories: [CS, Algorithm]
tags:
  [
    CS,
    Algorithm,
    TwoPointer
  ]
---

## 1. 투 포인터 (Two Pointers)

- 배열에서 두 개의 포인터를 사용하여 원하는 결과를 얻는 방법
- 두 개 포인터의 배치 방법
    - 같은 방향에서 시작 : 첫 번째 원소에 둘 다 배치
    - 서로 다른 방향에서 시작 : 첫 번째 원소와 마지막 원소에 배치
- 다중 for문의 복잡도를 좀 더 선형적으로 풀 수 있음

## 2. 투 포인터 예시

- 아래 배열에서 부분합이 9가 되는 구간을 찾는 방법
    - 기존 단순 for문 이용 방법
    
    <img width="496" alt="Untitled" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/9069712b-ea87-47fe-9860-b74ed4c5ccd6">

    
    - 투 포인터 방법
    
    <img width="401" alt="Untitled 1" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/216a5aa8-0f5b-4a87-ad04-f7b0585519b1">

    
    **(실습 : 투포인터를 이용하여 부분합 구하기)**
    
    ```java
    // 알고리즘 - 투 포인터
    // for-loop vs two pointers
    
    import java.util.Arrays;
    
    public class Main {
        public static int[] forLoop(int[] arr, int target) {
            int[] result = {-1, -1};
    
            for (int i = 0 ; i < arr.length ; i ++) {
                int sum = arr[i];
                for (int j = i + 1 ; j < arr.length ; j ++ ) {
                    if (sum == target) {
                        result[0] = i;
                        result[1] = j - 1;
                        break;
                    }
                    sum += arr[j];
                }
    
                if (sum == target) {
                    break;
                }
            }
    
            return result;
        }
    
        public static int[] twoPointers(int[] arr, int target) {
            int p1 = 0;
            int p2 = 0;
            int sum = 0;
            int[] result = {-1, -1};
    
            while (true) {
                if (sum > target) {
                    sum -= arr[p1++];
                } else if (p2 == arr.length) {
                    break;
                } else {
                    sum += arr[p2++];
                }
    
                if (sum == target) {
                    result[0] = p1;
                    result[1] = p2 - 1;
                    break;
                }
            }
            return result;
        }
    
        public static void main(String[] args) {
            int[] arr = {1, 2, 5, 3, 7, 2, 4, 3, 2};
            System.out.println(Arrays.toString(forLoop(arr, 9)));
            System.out.println(Arrays.toString(forLoop(arr, 14)));
            System.out.println();
    
            System.out.println(Arrays.toString(twoPointers(arr, 9)));
            System.out.println(Arrays.toString(twoPointers(arr, 14)));
        }
    }
    ```
    
    <img width="133" alt="Untitled 2" src="https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/bb57c6c6-25ad-477f-b53e-10bef36f7d47">
