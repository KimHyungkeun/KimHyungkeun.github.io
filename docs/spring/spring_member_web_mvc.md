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

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/4ba3b684-1a12-4608-9b9e-f762c176df70)

- 회원 가입 화면 ( “/members/new” )
![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/ed1b14a6-31ab-4d7b-b719-c632cf702a88)

- 회원 목록 화면 ( “/members” )
![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/f2e77fda-75b9-4b57-8aee-eca69d34d197)
