---
layout: default
title: Spring
nav_order: 1
parent: Spring Lecture
---

## 1. 스프링 부트 설정

1)  스프링부트 프로젝트 생성
https://start.spring.io/

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/daad6a35-69f9-48bc-8d63-89f316412e64/Untitled.png)

**Project** : Gradle Project

**Language** : Java

**Spring Boot** : 2.7.5

- SNAPSHOT은 아직 개발 중이라는 의미
- RC2는 정식 Release가 되지 않은 것
- 결론은 아무런 표시가 없는 2.7.5로 진행

**Packaging** : Jar

**Java** : 11

**Dependencies** : Spring Web, Thymleaf

GENERATE 진행 후, zip파일을 압축 해제

2) 사전 준비물

1. Java 11 (가급적 JDK 11로 설치할 것)
2. IntelliJ

## 2. IntelliJ에서 zip파일 압축 해제 폴더를 open

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5adc0c1f-1b49-4a1a-b829-06d4f90f894b/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d6073e43-649b-4f7b-afd6-034c9cdcc13b/Untitled.png)

- 막간의 IntelliJ Grade 빌드 시 설정
- (Windows 기준) Ctrl + Alt + S ⇒ (File → Settings → Gradle(검색))
- 하단의 **Build and run using**과 **Run tests using**을 “IntelliJ IDEA”로 설정
- 그러면, Run 하기 전에 바로 IntelliJ 상에서 빌드 됨

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8873a7ad-a402-4623-8919-f59e274751ec/Untitled.png)

- Default로 남겨두면 아래와 같이 Build폴더가 생성됨

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cc211304-5f86-4f0b-85ad-fdf405e29885/Untitled.png)