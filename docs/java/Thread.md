---
layout: default
title: 8. Thread
parent: Java
nav_order: 8
---

## 1. 특징

- 동작하고 있는 프로그램은 프로세스(Process)라고 부름
- 한 개의 프로세스는 한 개의 일을 진행한다
- Thread를 이용하면 프로세스 내에서 두 가지 또는 그 이상의 일을 진행

## 2. Thread 예제

```java
public class Sample extends Thread {
    int seq;

    public Sample(int seq) {
        this.seq = seq;
    }

    public void run() {
        System.out.println(this.seq + " thread start.");  // 쓰레드 시작
        try {
            Thread.sleep(1000);  // 1초 대기한다.
        } catch (Exception e) {
        }
        System.out.println(this.seq + " thread end.");  // 쓰레드 종료 
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {  // 총 10개의 쓰레드를 생성하여 실행한다.
            Thread t = new Sample(i);
            t.start();
        }
        System.out.println("main end.");  // main 메소드 종료
    }
}
```

- 결과

```java
0 thread start.
4 thread start.
6 thread start.
2 thread start.
main end.
3 thread start.
7 thread start.
8 thread start.
1 thread start.
9 thread start.
5 thread start.
0 thread end.
4 thread end.
2 thread end.
6 thread end.
7 thread end.
3 thread end.
8 thread end.
9 thread end.
1 thread end.
5 thread end.
```

## 3. Join

- 위 예제에서는 쓰레드 종료 전에 main 메소드가 종료되었음
- 이번엔 모든 쓰레드가 종료된 후에 main 메소드를 종료

```java
import java.util.ArrayList;

public class Sample extends Thread {
    int seq;
    public Sample(int seq) {
        this.seq = seq;
    }

    public void run() {
        System.out.println(this.seq+" thread start.");
        try {
            Thread.sleep(1000);
        }catch(Exception e) {
        }
        System.out.println(this.seq+" thread end.");
    }

    public static void main(String[] args) {
        ArrayList<Thread> threads = new ArrayList<>();
        for(int i=0; i<10; i++) {
            Thread t = new Sample(i);
            t.start();
            threads.add(t);
        }

        for(int i=0; i<threads.size(); i++) {
            Thread t = threads.get(i);
            try {
                t.join(); // t 쓰레드가 종료할 때까지 기다린다.
            }catch(Exception e) {
            }
        }
        System.out.println("main end.");
    }
}
```

- 결과

```java
0 thread start.
5 thread start.
2 thread start.
6 thread start.
9 thread start.
1 thread start.
7 thread start.
3 thread start.
8 thread start.
4 thread start.
0 thread end.
5 thread end.
2 thread end.
9 thread end.
6 thread end.
1 thread end.
7 thread end.
4 thread end.
8 thread end.
3 thread end.
main end.
```

출처 : [https://wikidocs.net/230](https://wikidocs.net/230)

## 4. Runnable

- Thread 객체를 만들 때 위의 예처럼 Thread를 상속하여 만들기도 하지만, 
보통은 Runnable 인터페이스 구현
- 왜냐하면, Thread 클래스를 상속하면 다른 클래스 상속이 불가
- Runnable 인터페이스는 run 메소드를 구현하도록 강제한다

```java
class Sum {
   int num;
   public Sum() {
	   num=0;
   }
   public void addNum(int n) {
	   num += n;
   }
   public int getNum() {
	   return num;
   }
}

class AdderThread extends Sum implements Runnable {
	
	int start, end;
	
	public AdderThread(int s, int e) {
		start=s;
		end=e;
	}
	
	public void run() {
		for(int i=start; i<=end; i++) {
			addNum(i);
		}
	}
	
}

/* Name of the class has to be "Main" only if the class is public. */
class myTest
{
	public static void main(String[] args) {

        AdderThread at1 = new AdderThread(1, 50);
        AdderThread at2 = new AdderThread(51, 100);
				// Thread의 생성자로 Runnable 인터페이스를 구현한 객체를 넘길 수 있는데 이 방법을 사용한 것이다
        Thread tr1 = new Thread(at1);
        Thread tr2 = new Thread(at2);
        tr1.start();
        tr2.start();
        
        try {
        	tr1.join();
        	tr2.join();
        } catch (InterruptedException e) {
        	e.printStackTrace();
        }
        System.out.println("1~100까지의 합 : " + (at1.getNum() + at2.getNum()));
		
	}
}
```

## 5. Thread의 특성

**1) 쓰레드의 스케쥴링(Scheduling)**

- 우선순위가 높은 쓰레드의 실행을 우선
- 동일한 우선순위의 쓰레드가 둘 이상 존재할 때는, CPU 할시간을 분배

**예제1)**

```java
class MessageSendingThread extends Thread {
    String message;
    
    public MessageSendingThread(String str) {
    	message=str;
    }
    public void run() {
    	for (int i=0;i<1000;i++) {
    		System.out.println(message+"("+getPriority()+")");
    	}
    }
}

/* Name of the class has to be "Main" only if the class is public. */
class myTest
{
	public static void main(String[] args) {
        MessageSendingThread tr1 = new MessageSendingThread("First");
        MessageSendingThread tr2 = new MessageSendingThread("Second");
        MessageSendingThread tr3 = new MessageSendingThread("Third");
    	tr1.start();
    	tr2.start();
    	tr3.start();
		
	}
}
```

- 결과 : 우선순위가 동일하면, CPU의 할당시간을 나눠 가진다

```java
Third(5)
Third(5)
Third(5)
Third(5)
Third(5)
Second(5)
Second(5)
Second(5)
Second(5)
Second(5)
...
```

**예제2)**

```java
class MessageSendingThread extends Thread {
    String message;
    
    public MessageSendingThread(String str, int prio) {
    	message=str;
    	setPriority(prio);
    }
    public void run() {
    	for (int i=0;i<1000;i++) {
    		System.out.println(message+"("+getPriority()+")");
    	}
    }
}

/* Name of the class has to be "Main" only if the class is public. */
class myTest
{
	public static void main(String[] args) {
        MessageSendingThread tr1 = new MessageSendingThread("First", Thread.MAX_PRIORITY); //상수 값으로 10
        MessageSendingThread tr2 = new MessageSendingThread("Second", Thread.NORM_PRIORITY); // 상수 값으로 5
        MessageSendingThread tr3 = new MessageSendingThread("Third", Thread.MIN_PRIORITY); // 상숙 값으로 1
    	tr1.start();
    	tr2.start();
    	tr3.start();
		
	}
}
```

- 결과 : 우선순위가 가장 높은 쓰레드가 종료되어야, 그 다음 우선순위의 쓰레드가 실행

```java
First(10)
First(10)
First(10)
...
Second(5)
...
Third(1)
```

예제3)

```java
class MessageSendingThread extends Thread {
    String message;
    
    public MessageSendingThread(String str, int prio) {
    	message=str;
    	setPriority(prio);
    }
    public void run() {
    	for (int i=0;i<100;i++) {
    		System.out.println(message+"("+getPriority()+")");
    		
    		try {
    			sleep(1);
    		} catch (InterruptedException e) {
    			e.printStackTrace();
    		}
    	}
    }
}

/* Name of the class has to be "Main" only if the class is public. */
class myTest
{
	public static void main(String[] args) {
        MessageSendingThread tr1 = new MessageSendingThread("First", Thread.MAX_PRIORITY);
        MessageSendingThread tr2 = new MessageSendingThread("Second", Thread.NORM_PRIORITY);
        MessageSendingThread tr3 = new MessageSendingThread("Third", Thread.MIN_PRIORITY);
    	tr1.start();
    	tr2.start();
    	tr3.start();
		
	}
}
```

- 결과 :  sleep 메소드 호출 시, 쓰레드는 CPU 할당이 불필요한 상황이 된다. 따라서, 다른 쓰레드에게, 할당된 CPU를 양보.

```java
Second(5)
First(10)
Third(1)
First(10)
Third(1)
Second(5)
Second(5)
First(10)
Third(1)
Third(1)
...
```

**2) Thread의 라이프 사이클**

![image](https://user-images.githubusercontent.com/12759500/233837653-190da766-76cc-4e99-a943-4e17c893e627.png)

- New
    - 쓰레드 클래스가 new를 통해 인스턴스화 된 상태
    - JVM에 관리되는 쓰레드 상태는 아직 아님
- Runnable/Running
    - start 메소드가 호출되면, 해당 쓰레드는 비로소 Runnable이 됨
    - Runnable 상태에 있다가, 스케쥴러에 의해 실행의 대상으로 되어야 비로소 run메소드 호출
- Blocked
    - 실행중인 쓰레드가 sleep 또는 join 메소드를 호출하거나, 
    CPU할당이 필요치 않는 입출력 연산시 실행
    - 이 상태에서는 스케쥴러의 선택을 받을 수 없음
    - Block상태로 된 원인이 제거되어야만, Runnable이 실행
    - 예를 들어, sleep으로 block되었다면 sleep이 반환되어야 block이 풀림
- Dead
    - run 메소드의 실행이 완료되어서 run 메소드를 빠져나오면, 해당 쓰레드는 Dead가 된다
    - 쓰레드 실행을 위해 할당 받았던 메모리를 비롯한 각종 쓰레드 관련 정보가 완전히 사라짐
    - 한 번 Dead가 되고나면, 다시는 Runnable이 되지 못함
    

**3) 쓰레드의 메모리 구성**

- 모든 쓰레드는 자신의 stack을 할당 받는다
- 그러나, 힙과 메소드 영역은 모든 쓰레드가 공유하게 된다

예제)

```java
class Sum {
   int num;
   public Sum() {
	   num=0;
   }
   public void addNum(int n) {
	   num += n;
   }
   public int getNum() {
	   return num;
   }
}

class AdderThread extends Thread {
	Sum sumInst;
	int start, end;
	
	public AdderThread(Sum sum, int s, int e) {
		sumInst = sum;
		start=s;
		end=e;
	}
	
	public void run() {
		for(int i=start; i<=end; i++) {
			sumInst.addNum(i);
		}
	}
	
}

/* Name of the class has to be "Main" only if the class is public. */
class myTest
{
	public static void main(String[] args) {
        Sum s = new Sum(); // 31행
        AdderThread at1 = new AdderThread(s, 1, 50); //32행
        AdderThread at2 = new AdderThread(s, 51, 100); //33행
        at1.start();
        at2.start();
        
        try {
        	at1.join();
        	at2.join();
        } catch (InterruptedException e) {
        	e.printStackTrace();
        }
        System.out.println("1~100까지의 합 : " + s.getNum());
		
	}
}
```

- 결과 :
    - 31행에서 생성한 인스턴스를, 32행과 33행에서 생성하는 쓰레드 인스턴스에 생성자를 통해 전달. 따라서, 2개의 쓰레드는 31행에서 생성한 인스턴스에 접근 가능

```java
1~100까지의 합 : 5050
```
