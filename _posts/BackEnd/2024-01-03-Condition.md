---
title: 조건문
date: 2024-01-03 00:00:00 +00:00
categories: [BackEnd, Java]
tags:
  [
    BackEnd,
    Java
  ]
---

### 1. 조건문 - if

- 조건에 따라 무엇을 실행할지 판단하는 분기 구조

```java
if (조건문1) {
	조건문 1을 만족할 대 실행할 내용;
} else if (조건문2) {
	조건문 2를 만족할 때 실행할 내용;
} else {
  그 외의 상황에서 실행할 내용;
}
```

### 2. 조건문 - switch

- 입력 값에 따라 어떤 case를 실행할지 판단하는 분기 구조

```java
switch(입력값) {
  case 입력값1 : 
    실행할 내용;
    break;
  case 입력값2 :
    실행할 내용;
    break;
  default:
    실행할내용;
    break;
}
```