---
title: BackTracking (백트래킹)
date: 2024-01-31 00:00:00 +00:00
categories: [CS, Algorithm]
tags:
  [
    CS,
    Algorithm,
    BackTracking
  ]
---

## 1. 백트래킹

- 모든 경우의 수를 탐색하며 최적해를 구하는 과정에서 유망하지 않은 쪽은 더 이상 구하지 않는 방법
- 용어
    - 유망(Promising) : 해가 될 가능서이 있는 경어 유망하다고 함
    - 가치치기 (Pruning) : 해가 될 가능성이 없는 경우 해당 노드를 제외
    - 백트래킹 (Backtracking) : 유망하지 않은 쪽으로 가지 않고 되돌아 오는

## 2. 백트래킹 예시

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/733e9a5a-c2f0-40a2-98c9-8f5a716d6e7c)


![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/bdaf4e68-cb5f-4084-a5ce-b64edb488e00)


![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/5ee69c38-4f6b-4e84-958c-7756dcd5b9ed)


**(실습 : 백트래킹 예제 - nQueen)**

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/53472731-3f40-4203-9e88-0b98f6db7e54)

```java
// 알고리즘 - 백트래킹

public class Main {

    static int n = 4;
    static int[] board = new int[n];
    static int cnt;

    public static int nQueen(int row) {
        if (row == n) {
            cnt++;
            for (int i = 0 ; i < n ; i++) {
                System.out.print(board[i] + " ");
            }
            System.out.println();
            return cnt;
        }

        for (int i = 0 ; i < n ; i++) {
            board[row] = i;

            //promising
            if (isPromising(row)) {
                nQueen(row + 1);
            }
        }
        return cnt;
    }

    public static boolean isPromising(int row) {
        for (int i = 0 ; i < row ; i++) {
            if (board[row] == board[i] || row - i == Math.abs(board[row] - board[i])) {
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) {
        // Test code
        System.out.println("경우의 수: " + nQueen(0));  // 2
    }
}
```
