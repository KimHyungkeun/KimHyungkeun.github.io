---
title: 예외 (Exception)
date: 2024-01-08 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

## 1. 예외 (Exception)

- 정상적이지 않은 Case
    - 0으로 나누기
    - 배열의 인덱스 초과
    - 없는 파일 열기

```java
int a = 1 / 0
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/03532532-23c7-47b8-a81e-9945a9eab2dd)


## 2. 예외처리 (Exception Handling)

- 정상적이지 않은 Case에 대한 적절한 처리 방법

```java
try{
	...
} catch (예외 case 1) {
	...
} catch (예외 case 2) {
	...
}
```

```java
int a = 0;
try{
	a = 5 / 0;
} catch (ArithmeticException e) {
	System.out.println(e);
}
```

## 3. finally

- 예외 발생 여부와 관계없이 항상 실행되는 부분

```java
try{
	예외가 발생될 수도 있는 부분
} catch (예외 case 1) {
	예외 case1이 발생해야 실행되는 부분
} finally {
	항상 실행되는 부분;
}
```

## 4. throw

- throw : 예외를 발생 시킴
- throws : 예외를 전가 시킴

```java
함수이름 () {
	throw new Exception();
} 

함수이름 () throws Exception {
	...
}
```

---

**(실습1 : DividebyZero 예외)**

```java
// Java 프로그래밍 - 예외 처리

    public static void main(String[] args) throws IOException {

//      1. 예외
//      1-1. 0으로 나누기
        System.out.println("== 0으로 나누기 ==");
      //int a = 5 / 0;
			try {
            int a = 5 / 0;
        } catch (ArithmeticException e) {
            System.out.println("0으로 나누기 예외 발생");
            System.out.println("e = " + e);
        } finally {
            System.out.println("1-1 연습 종료");
        }

    }

}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/f4f6d906-e10d-4c8c-8d9e-d4c2ecf95021)


**(실습2: ArrayIndexOutOfBoundsException)**

```java
// Java 프로그래밍 - 예외 처리

    public static void main(String[] args) throws IOException {

//      1-2. 배열 인덱스 초과
        System.out.println("== 배열 인덱스 초과 ==");
        int[] b = new int[4];
//        b[4] = 1;
        try {
            b[4] = 1;
        } catch(ArrayIndexOutOfBoundsException e) {
            System.out.println("e = " + e);
        }

        try {
            b[4] = 1;
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("인덱스 초과!");
            System.out.println("e = " + e);
        }

    }

}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/7c088bf9-9d48-4a43-b591-5600b572132e)


**(실습3 : throw)**

```java
// Java 프로그래밍 - 예외 처리

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

class NotTenException extends RuntimeException {}

public class Main {

    public static boolean checkTen(int ten) {
        if (ten != 10) {
            return false;
        }
        return true;
    }

    public static boolean checkTenWithException(int ten) {
        try {
            if (ten != 10) {
                throw new NotTenException();
            }
        } catch (NotTenException e) {
            System.out.println("e = " + e);
            return false;
        }
        if (ten != 10) {
            throw new NotTenException();
        }
        return true;
    }

    public static void main(String[] args) throws IOException {

//      2. throw, throws
        System.out.println("== checkTen ==");
        boolean checkResult = Main.checkTen(10);
        System.out.println("checkResult = " + checkResult);

        System.out.println("== checkTenWithException ==");
        checkResult = Main.checkTenWithException(9);
				System.out.println("checkResult = " + checkResult);
   
    }

}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/1778d1da-0f7c-4871-8b68-c0e0edd98b20)


**(실습4 : throws)**

```java
// Java 프로그래밍 - 예외 처리

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

class NotTenException extends RuntimeException {}

public class Main {

    public static boolean checkTenWithThrows(int ten) throws NotTenException {
        if (ten != 10) {
            throw new NotTenException();
        }

        return true;
    }

    public static void main(String[] args) throws IOExcep
        System.out.println("== checkTenWithThrows ==");

        try {
            checkResult = checkTenWithThrows(5);
        } catch (NotTenException e) {
            System.out.println("e = " + e);
        }
        System.out.println("checkResult = " + checkResult);

    }

}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/18ebf95d-4897-472d-a867-49fdb9187608)


