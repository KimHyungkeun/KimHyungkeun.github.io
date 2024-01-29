---
title: 점화식과 재귀함수
date: 2024-01-29 00:00:00 +00:00
categories: [CS, Math]
tags:
  [
    CS,
    Math
  ]
---

## 1. 점화식

- 어떤 수열의 일반항을 그 이전의 항들을 이용하여 정의한 식
- 예시) 피보나치 수열
    - 1,1,2,3,5,8,13,…
    - F1=F2=1, F(n+2) = F(n+1) + F(n)

## 2. 재귀함수

- 어떤 함수가 자신을 다시 호출하여 작업을 수행하는 방식
    
    ```java
    반환타입 함수이름 (매개변수) {
    	종료조건
    	…
    	함수이름( )
    }
    ```
    

**(실습 : 점화식 및 재귀함수)**

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/365263b7-2f76-4af4-baa4-a6338372d606)


```java
// 기초 수학 - 점화식과 재귀함수

public class Main {

    static int recursion1 (int n) {
        if (n == 1) {
            return 1;
        }
        return 3 * recursion1(n - 1);
    }

    static int recursion2 (int n) {
        if (n == 1) {
            return 1;
        }
        return n * recursion2(n - 1);
    }

    static int recursion3 (int n) {
        if (n <= 2) {
            return 1;
        }
        return recursion3(n-1) + recursion3(n-2);
    }

    public static void main(String[] args) {
        
//      점화식 -> 반복문, 재귀함수
        System.out.println("== 점화식/재귀함수 연습1 ==");
//      1, 3, 9, 27, ... 의 n번째 수
        int n = 4;
        int result = 1;
        for (int i = 0; i < n; i++) {
            if (i == 0) {
                result = 1;
            } else {
                result *= 3;
            }
        }
        System.out.println(result);

        System.out.println("== 점화식/재귀함수 연습2 ==");
//      1, 2, 3, 4, 5, 6, ... 의 n번째 까지의 합
        n = 5;
        result = 0;
        for (int i = 1; i < n + 1; i++) {
            result += i;
        }
        System.out.println(result);

        System.out.println("== 점화식/재귀함수 연습3 ==");
//      1, 1, 2, 3, 5, 8, 13, ...의 n번 째 수
        n = 6;
        result = recursion3(n);
        System.out.println(result);

    }
}
```
