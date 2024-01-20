---
title: 입출력
date: 2024-01-05 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

## 1. 콘솔 입력

- 입출력 방식 중 콘솔 입력 방법

```java
System.in.read();
InputStreamReader reader 
BufferedReader br 
Scanner 
```

```java
public static void referInputStream() throws IOException {
//      System.in
        System.out.println("== System.in ==");
        System.out.println("입력 :");
        int a = System.in.read() - '0';
        System.out.println("a = " + a);
        System.in.read(new byte[System.in.available()]);

//      InputStreamReader
        System.out.println("== InputStreamReader ==");
        InputStreamReader reader = new InputStreamReader(System.in);
        char[] c = new char[3];
        System.out.print("입력: ");
        reader.read(c);
        System.out.println(c);

//      BufferedReader
        System.out.println("== BufferedReader ==");
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        System.out.print("입력: ");
        String s1 = br.readLine();
        System.out.println("s1 = " + s1);

    }
```

## 2. 콘솔 출력

- 입출력 방식 중 콘솔 출력 방법

```java
System.out.println(...);
System.out.print(...);
System.out.print(...);
```

```java
public static void main(String[] args) throws IOException {
	System.out.println("== 출력 ==");
	System.out.println("Hello");
	System.out.println("World!");

	// int 출력
	System.out.printf("%d\n", 10);

	// 8진수 출력
	System.out.printf("%o\n", 10);

	// 16진수 출력
	System.out.printf("%x\n", 10);
	
	// 실수형(float) 출력
	System.out.printf("%f\n", 5.2f);

	// char 출력
	System.out.printf("%c\n", 'A');

	// String 출력
	System.out.printf("%s\n", "Hello");

	// 자릿수를 5자리로 맞춘 후, 정수 출력
	System.out.printf("%5d\n", 123);

	// 소수 2자리수 까지 출력
	System.out.printf("%.2f\n", 1.12645123f);
}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/c0abd040-bcd5-4f3c-ab07-723f5dd6efa1)


## 3. 파일 출력

- 입출력 방식 중 파일로 출력하는 방법

```java
FileOutputStream
FileWriter
PrintWriter
```

```java
// Java 프로그래밍 - 입출력_2

import java.io.*;

public class Main {

    public static void main(String[] args) throws IOException {
//      1. 파일 쓰기
//      FileWriter
        FileWriter fw = new FileWriter("./memo.txt");
        String memo = "헤드 라인\n";
        fw.write(memo);

        memo = "1월 1일 날씨 맑음\n";
        fw.write(memo);

        fw.close();

//      PrintWriter
        PrintWriter pw = new PrintWriter("./memo2.txt");
        memo = "헤드 라인";
        pw.println(memo);

        memo = "1월 1일 날씨 맑음";
        pw.println(memo);

        pw.close();

//      파일 이어 쓰기
        FileWriter fw2 = new FileWriter("./memo.txt", true);

        memo = "1월 2일 날씨 완전 맑음\n";
        fw2.write(memo);
        fw2.close();

        PrintWriter pw2 = new PrintWriter(new FileWriter("./memo2.txt", true));
        pw2.println(memo);
        pw2.close();

    }
}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/bcce7385-5b23-4c72-9746-88178ea7a79b)


## 4. 파일 입력

- 입출력 방식 중 파일로부터 입력 받는 방법

```java
FileInputStream
BufferdReader
```

```java
// Java 프로그래밍 - 입출력_2

import java.io.*;

public class Main {

    public static void main(String[] args) throws IOException {

//      2. 파일 입력
        BufferedReader br = new BufferedReader(new FileReader("./memo.txt"));

        while(true) {
            String line = br.readLine();

            if (line == null) {
                break;
            }

            System.out.println(line);
        }

        br.close();
    }
}
```

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/c92be1b8-fb01-464f-a12a-6501f90c7bfd)
