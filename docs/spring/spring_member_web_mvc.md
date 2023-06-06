---
layout: default
title: 5. 회원 관리 예제 - 웹 MVC 개발
nav_order: 5
parent: Spring Lecture
grand_parent: Spring
permalink: /docs/spring/spring_lecture/spring_member_web_mvc
---

# 5. 회원 관리 예제 - 웹 MVC 개발

## 1. 회원 웹 기능 - 홈 화면 추가

1) 홈 컨트롤러 추가

```java
package hello.hellospring.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {
	@GetMapping("/")
	public String home() {
	return "home";
	}
}
```

2) 회원 관리 용 홈

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
	<body>
	<div class="container">
		<div>
		<h1>Hello Spring</h1>
			<p>회원 기능</p>
			<p>
			<a href="/members/new">회원 가입</a>
			<a href="/members">회원 목록</a>
			</p>
		</div>
	</div> <!-- /container -->
	</body>
</html>
```

## 2. 회원 웹 기능 - 등록

1) 회원 등록 홈 컨트롤러

```java
@Controller
public class MemberController {
	private final MemberService memberService;

	@Autowired
	public MemberController(MemberService memberService) {
		this.memberService = memberService;
	}

	@GetMapping(value = "/members/new")
	public String createForm() {
		return "members/createMemberForm";
	}
}
```

2) 회원 등록 폼 HTML

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
	<body>
		<div class="container">
			<form action="/members/new" method="post">
				<div class="form-group">
				<label for="name">이름</label>
				<input type="text" id="name" name="name" placeholder="이름을
				입력하세요">
				</div>
				<button type="submit">등록</button>
			</form>
		</div> <!-- /container -->
	</body>
</html>
```

3) 웹 등록 화면에서 데이터를 전달 받은 폼 객체

```java
package hello.hellospring.controller;

public class MemberForm {
	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
```

4) 회원 컨트롤러에서 회원을 실제 등록하는 기능

```java
@PostMapping(value = "/members/new")
public String create(MemberForm form) {
	Member member = new Member();
	member.setName(form.getName());
	memberService.join(member);
	return "redirect:/";
}
```

## 3. 회원 웹 기능 - 조회

1) 회원 컨트롤러에서 조회

```java
@GetMapping(value = "/members")
public String list(Model model) {
	List<Member> members = memberService.findMembers();
	model.addAttribute("members", members);
	return "members/memberList";
}
```

2) 회원 리스트 HTML

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
	<body>
		<div class="container">
			<div>
				<table>
					<thead>
						<tr>
							<th>#</th>
							<th>이름</th>
						</tr>
					</thead>
					<tbody>
						<tr th:each="member : ${members}">
							<td th:text="${member.id}"></td>
							<td th:text="${member.name}"></td>
						</tr>
					</tbody>
				</table>
			</div>
		</div> <!-- /container -->
	</body>
</html>
```

- 홈 화면

![Untitled](5%20%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%A8%E1%84%8C%E1%85%A6%20-%20%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%205d36beda5b594eacaf9acf805ef7d86e/Untitled.png)

- 회원 가입 화면 ( “/members/new” )

![Untitled](5%20%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%A8%E1%84%8C%E1%85%A6%20-%20%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%205d36beda5b594eacaf9acf805ef7d86e/Untitled%201.png)

- 회원 목록 화면 ( “/members” )

![Untitled](5%20%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%A8%E1%84%8C%E1%85%A6%20-%20%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%205d36beda5b594eacaf9acf805ef7d86e/Untitled%202.png)
