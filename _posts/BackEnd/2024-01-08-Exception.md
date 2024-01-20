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

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/1fb17b7f-dece-4885-afc5-0b38cb0f1a02/Untitled.png)

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

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/7aaa5952-c0c6-4204-ab6d-cec4d87d17e5/Untitled.png)

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

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/b0f65b5c-9a42-44b6-999c-c5fa8a352043/Untitled.png)

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

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/8975e213-041d-4656-9314-6bb6471a0a60/Untitled.png)

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

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/43f45988-4c0f-451e-b113-a8c5500ed732/Untitled.png)

