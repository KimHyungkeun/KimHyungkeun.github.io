---
layout: default
title: 2. 스프링 웹 개발 기초
nav_order: 2
parent: Spring Lecture
grand_parent: Spring
permalink: /docs/spring/spring_lecture/spring_web_devel_base
---

# 2. 스프링 웹 개발 기초

# 1. 정적 컨텐츠

1) 정적 컨텐츠

[https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)

```html
// hello-static.html을 java/main/resources/static에 진행
<!DOCTYPE HTML>
<html>
<head>
  <title>static content</title>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
정적 컨텐츠 입니다.
</body>
</html>
```

ex) localhost:8080/hello-static.html

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/300641b5-a681-414e-a9be-94a01e1d8638)

# 2. MVC와 템플릿 엔진

2) MVC와 템플릿 엔진

- MVC : Model, View, Controller

- Controller

```java
@Controller
public class HelloController {
	@GetMapping("hello-mvc")
	public String helloMvc(@RequestParam("name") String name, Model model) {
	model.addAttribute("name", name);
	return "hello-template";
	}
}
```

- View

```html
// resources/templates/hello-template.html
<html xmlns:th="http://www.thymeleaf.org">
	<body>
	<p th:text="'hello ' + ${name}">hello! empty</p>
	</body>
</html>
```

ex) [http://localhost:8080/hello-mvc?name=](http://localhost:8080/hello-mvc?name=)spring

![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/4b26ea0f-80d2-41bb-ad64-55236097b094)

# 3. API

```java
@Controller
public class HelloController {
	@GetMapping("hello-api")
	@ResponseBody

	public Hello helloApi(@RequestParam("name") String name) {
		Hello hello = new Hello();
		hello.setName(name);
		return hello;
	}

	static class Hello {
		private String name;
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
	
	}

}
```

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/94f479a7-816d-49c3-9023-a156a2c80233)

- 참고 : 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 ‘HttpMessageConverter’가 선택 된다.
- Jackson : 
Spring에서 기본적으로 쓰는 라이브러리로 여기서 JSON객체로 바꿈. 
(Jackson 외에도, Google에서 만든 Gson이라는 라이브러리도 존재)
