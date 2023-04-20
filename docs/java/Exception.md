---
layout: default
title: 2. Exception
parent: Java
nav_order: 2
---

## 1. 오류(error)와 예외(Exception)

- 오류(error) : 시스템 레벨에서 프로그램에 심각한 문제를 야기한 것
⇒ 오류는 개발자가 미리 예측하여 처리할 수가 없음

- 예외(exception) : 프로그램 실행 중 비정상적인 작동으로 인해 종료
- ⇒ 예외는 미리 발생할 수 있는 상황을 예측 가능

## 2. 예외처리(Exception Handling)

```bash
try {
    예외를 처리하길 원하는 실행 코드;
} catch (e1) {
    e1 예외가 발생할 경우에 실행될 코드;
} catch (e2) {
    e2 예외가 발생할 경우에 실행될 코드;
}

...

finally {
    예외 발생 여부와 상관없이 무조건 실행될 코드;
}
```

- try : 기본적으로 맨 먼저 실행되는 코드. 여기에서 발생한 예외는 catch에서 잡음
- catch : try 블록에서 발생한 예외코드나 예외 객체를 인수로 전달받아 그 처리르 담당
- finally 블록: try블록에서 예외가 발생하건 안하건, 마지막에 무조건 처리
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00c451ce-5fdb-4a83-b50d-b3f4dca7c4ed/Untitled.png)
    
    **1) 예외처리 코드** 
    
    (Before)
    
    ```bash
    class FoolException extends RuntimeException {
    }
    
    public class Sample {
        public void sayNick(String nick) {
            if("fool".equals(nick)) {
                throw new FoolException();
            }
            System.out.println("당신의 별명은 "+nick+" 입니다.");
        }
    
        public static void main(String[] args) {
            Sample sample = new Sample();
            sample.sayNick("fool");
            sample.sayNick("genious");
        }
    }
    
    // 결과
    Exception in thread "main" FoolException
        at Sample.sayNick(Sample.java:7)
        at Sample.main(Sample.java:14)
    ```
    
    (After)
    
    ```bash
    class FoolException extends Exception {
    }
    
    public class Sample {
        public void sayNick(String nick) {
            try {
                if("fool".equals(nick)) {
                    throw new FoolException();
                }
                System.out.println("당신의 별명은 "+nick+" 입니다.");
            }catch(FoolException e) {
                System.err.println("FoolException이 발생했습니다.");
            }
        }
    
        public static void main(String[] args) {
            Sample sample = new Sample();
            sample.sayNick("fool");
            sample.sayNick("genious");
        }
    }
    ```
    
    **2) 예외 던지기 (throw)**
    
    ```bash
    class FoolException extends Exception {
    }
    
    public class Sample {
        public void sayNick(String nick) throws FoolException {
            if("fool".equals(nick)) {
                throw new FoolException();
            }
            System.out.println("당신의 별명은 "+nick+" 입니다.");
        }
    
        public static void main(String[] args) {
            Sample sample = new Sample();
            try {
                sample.sayNick("fool");
                sample.sayNick("genious");
            } catch (FoolException e) {
                System.err.println("FoolException이 발생했습니다.");
            }
        }
    }
    ```
    
    **3) 트랜잭션(Transaction)**
    
    ex) 쇼핑몰 운영자는 포장, 영수증발행, 발생을 모두 성공해야한다
    
    만일, 하나라도 실패하면 3가지를 모두 취소하고 상품 발송 전으로 되돌아 갈 것이다
    
    ```bash
    상품발송() {
        try {
            포장();
            영수증발행();
            발송();
        }catch(예외) {
            모두취소();  // 하나라도 실패하면 모두 취소한다.
        }
    }
    
    포장() throws 예외 {
       ...
    }
    
    영수증발행() throws 예외 {
       ...
    }
    
    발송() throws 예외 {
       ...
    }
    ```
    
    ## 3. 예외 클래스
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c3b590e3-ff88-4913-a84c-0a648eec867f/Untitled.png)
    
    ```
    ArithmeticException 
    어떤 수를 0으로 나누는 것과 같이 비정상 계산 중 발생
    
    NullPointerException 
    NULL 객체 참조시 발생 
    
    IllegalArgumentException
    메소드의 전달 인자값이 잘못될 경우 발생 
    
    IllegalStateException 
    객체의 상태가 메소드 호출에는 부적합할 경우 발생
    
    IndexOutOfBoundsException 
    배열의 index 값이 범위를 넘어갈 경우 발생 
    
    UnsupportedOperationException
    객체가 메소드를 지원하지 않은 경우 발생 
    
    SecurityException
    보안 위반 발생 시 보안 관리 프로그램에서 발생 
    
    ProviderException
    구성 공급자 오류시 발생 
    
    NoSuchElementException
    구성요소가 그 이상 없는 경우 발생 
    
    ArrayStoreException
    객체 배열에 잘못된 객체 유형 저장시 발생 
    
    ClassCastException
    클래스 간의 형 변환 오류시 발생 
    
    EmptyStackException
    스택이 비어있는데 요소를 제거하려고 할시 발생
    
    ClassNotFoundException
    지정된 클래스가 없는 경우 발생 
    
    FileNotFoundException
    존재하지 않는 파일의 이름을 입력했을 경우 
    
    DataFormatException
    입력한 데이터 형식이 잘못된 경우 발생 
    
    ```