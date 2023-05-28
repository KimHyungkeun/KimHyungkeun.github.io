---
layout: default
title: Spring
nav_order: 1
parent: Spring Lecture
---

## 1. 스프링 부트 설정

1)  스프링부트 프로젝트 생성
https://start.spring.io/

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/0efebbc2-5928-48a3-ad31-3a44d9ca9891)

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

![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/b52475b5-edbb-48a2-a1bd-b33f95ca84e6)

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/4649ddc0-f9a8-40d3-a1e4-98587112d924)

- 막간의 IntelliJ Grade 빌드 시 설정
- (Windows 기준) Ctrl + Alt + S ⇒ (File → Settings → Gradle(검색))
- 하단의 **Build and run using**과 **Run tests using**을 “IntelliJ IDEA”로 설정
- 그러면, Run 하기 전에 바로 IntelliJ 상에서 빌드 됨
![Untitled 3](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/5d8ed473-05de-452b-bb4a-da41e17b4edf)

- Default로 남겨두면 아래와 같이 Build폴더가 생성됨
![Untitled 4](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/2d8882b8-89ba-45d1-b5b8-83c3c36d1fc9)
