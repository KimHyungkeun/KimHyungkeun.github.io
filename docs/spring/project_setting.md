---
layout: default
title: 1. 프로젝트 환경 설정
nav_order: 1
parent: Spring Lecture
grand_parent: Spring
permalink: /docs/spring/spring_lecture/project_setting
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

## 3. 스프링 부트 라이브러리

spring-boot-starter-web
spring-boot-starter-tomcat: 톰캣 (웹서버)
spring-webmvc: 스프링 웹 MVC
spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)
spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
spring-boot
spring-core
spring-boot-starter-logging
logback, slf4j

## 4. 테스트 라이브러리

spring-boot-starter-test
junit: 테스트 프레임워크
mockito: 목 라이브러리
assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
spring-test: 스프링 통합 테스트 지원

## 5. View 환경 설정

```html
<!-- src/main/java/resources/templates/hello.html -->
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <title>Hello</title>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}">안녕하세요, 손님</p>
</body>
</html>
```

```java
// src/main/java/hello/hellospring/controller
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model) {
        model.addAttribute("data", "hello!!");
        return "hello";
    }
}
```

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/fa675b67-3230-4c05-807b-4af3966841c7)

- 컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(”viewResolver”)가 화면을 찾아서 처리
- 스프링 부터 템플릿엔진 기본 viewName 매핑
- resources:/templates + {ViewName} + ‘.html’
- 참고 : spring-boot-devtools 라이브러리를 추가하면, html 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능하다.
- 인텔리J 컴파일 방법: 메뉴 build Recompile

## 6. build 하기

```bash
$ ./gradlew build
$ cd build/libs
$ java -jar hello-spring-0.0.1-SNAPSHOT.jar
```
