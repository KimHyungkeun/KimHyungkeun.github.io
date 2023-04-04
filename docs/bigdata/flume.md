---
layout: default
title: Flume
parent: BigData
nav_order: 6
---

# Flume 설치 과정

Flume  : [https://earthconquest.tistory.com/231](https://earthconquest.tistory.com/231)
Java : [https://it-serial.tistory.com/entry/Linux-CentOS-7-Oracle-JAVA-설치-JDK-환경-변수-설정](https://it-serial.tistory.com/entry/Linux-CentOS-7-Oracle-JAVA-%EC%84%A4%EC%B9%98-JDK-%ED%99%98%EA%B2%BD-%EB%B3%80%EC%88%98-%EC%84%A4%EC%A0%95)

# 1. Java JDK 설치

## 1) 설치하려는 JDK Version 확인 (220524 기준 Java JDK 1.8)

```bash
$ yum list java *jdk-devel
```

![Untitled](https://user-images.githubusercontent.com/12759500/229800639-97d35475-1e57-4c74-aa08-03a9bb36f7a3.png)

## 2) JDK 설치

```bash
$ yum -y install java-1.8.0-openjdk-devel.x86_64
```

## 3) javac 위치 확인

```bash
$ which javac
```

![Untitled 1](https://user-images.githubusercontent.com/12759500/229800706-a4838b84-0ea9-40b9-9015-1ba6ac532ff4.png)

## 4) readlink를 이용해 javac의 원본 위치 파악

```bash
$ readlink -f /usr/bin/javac
```

![Untitled 2](https://user-images.githubusercontent.com/12759500/229800935-74cacad3-8d54-4356-8167-e8bd6d9153c6.png)

## 5) 환경변수 설정

```bash
$ vi /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.332.b09-1.el7_9.x86_64/
$ source /etc/profile
```

## 6) 환경변수 확인

```bash
$ echo $JAVA_HOME
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.332.b09-1.el7_9.x86_64/
```

![Untitled 3](https://user-images.githubusercontent.com/12759500/229800982-bf22ac04-5800-4e79-ae65-a8524a3fea27.png)

## 7) 설치 확인

```bash
$ java -version
openjdk version "1.8.0_332"
OpenJDK Runtime Environment (build 1.8.0_332-b09)
OpenJDK 64-Bit Server VM (build 25.332-b09, mixed mode)
```

![Untitled 4](https://user-images.githubusercontent.com/12759500/229801005-c3ca042b-4ce2-45c2-abd0-60bb99968c52.png)

# 2. Flume 설치

## 1) Flume Download (220524 기준 Flume 1.9.0)

[https://flume.apache.org/download.html](https://flume.apache.org/download.html) 참고

```bash
$ mkdir /usr/local/application
$ cd /usr/local/application
$ wget **[https://dlcdn.apache.org/flume/1.9.0/apache-flume-1.9.0-bin.tar.gz](https://dlcdn.apache.org/flume/1.9.0/apache-flume-1.9.0-bin.tar.gz)**
$ tar -xzf apache-flume-1.9.0-bin.tar.gz
$ mv apache-flume-1.9.0-bin.tar.gz flume
```

## 2) 환경변수 설정

```bash
$ vi /etc/profile
```

```bash
# 맨 하단에 아래의 내용을 입력

export FLUME_HOME=/usr/local/application/flume
export PATH=$PATH:$FLUME_HOME/bin
export CLASSPATH=$CLASSPATH:$FLUME_HOME/lib/*.jar
```

```bash
$ source /etc/profile
```

## 3) 설치 확인

```bash
$ flume-ng version
```

```bash
Flume 1.9.0
Source code repository: https://git-wip-us.apache.org/repos/asf/flume.git
Revision: d4fcab4f501d41597bc616921329a4339f73585e
Compiled by fszabo on Mon Dec 17 20:45:25 CET 2018
From source with checksum 35db629a3bda49d23e9b3690c80737f9
```

![Untitled 5](https://user-images.githubusercontent.com/12759500/229801077-225ca62c-2a16-425a-a5ec-5b0770cf2f29.png)
