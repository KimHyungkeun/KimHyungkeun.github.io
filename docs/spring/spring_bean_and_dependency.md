---
layout: default
title: 4. 스프링 빈과 의존 관계
nav_order: 4
parent: Spring Lecture
grand_parent: Spring
permalink: /docs/spring/spring_lecture/spring_bean_and_dependency
---

# 4. 스프링 빈과 의존관계

- 스프링 빈을 등록하는 2가지 방법
    - 컴포넌트 스캔과 자동 의존관계 설정
    - 자바 코드로 직접 스프링 빈 등록 하기

## 1. 컴포넌트 스캔과 자동 의존관계 설정

### 1) 회원 컨트롤러에 의존관계 추가

```java
package hello.hellospring.controller;
import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class MemberController {
	private final MemberService memberService;

	@Autowired
	public MemberController(MemberService memberService) {
		this.memberService = memberService;
	}
}
```

- 생성자에 @Autowired가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다.
- 이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI(Dependency Injection, 의존성 주입) 이라고 한다

### 2) 오류 발생

```
Consider defining a bean of type 'hello.hellospring.service.MemberService' in
your configuration.
```

이유인 즉슨, memberService가 스프링 빈 으로 미등록 되어 있다.

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/bdadb588-abf7-4d9c-b3c4-ac67ac961a50)

 **컴포넌트 스캔 원리**

- @Component annotation이 있으면 스프링 빈으로 자동 등록
- @Controller가 스프링 빈으로 자동 등록된 이유는 컴포넌트 스캔 때문
- @Component를 포함하는 다음 Annotation도 스프링 빈으로 자동 등록
    - @Controller
    - @Service
    - @Repository

### 3) 회원 서비스 스프링 빈 등록

```java
@Service
public class MemberService {
	private final MemberRepository memberRepository;

	@Autowired
	public MemberService(MemberRepository memberRepository) {
		this.memberRepository = memberRepository;
	}
}
```

- 참고 : 생성자에 @Autowired를 사용하면 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입.
- 생성자가 1개만 있으면 @Autowired 생략 가능

### 4) 회원 레포지토리 스프링 빈 등록

```java
@Repository
public class MemoryMemberRepository implements MemberRepository {}
```

### 5) 스프링 빈 등록 이미지

![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/fc69fb2b-e8e5-45a2-b619-bb37aace1a2d)

- memberService와 memberRepository가 스프링 컨테이너에 스프링 빈으로 등록
- 참고 : 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 Singleton으로 등록한다(유일하게 하나만 등록해서 공유)
    
    ⇒ 따라서, 같은 스프링 빈이면 모두 같은 인스턴스이다. 설정으로 싱글톤이 아니게 설정은 가능하나, 대부분 싱글톤을 사용
    

## 2. 자바 코드로 직접 빈 등록

```java
package hello.hellospring;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class SpringConfig {
	@Bean
	public MemberService memberService() {
		return new MemberService(memberRepository());
	}

	@Bean
	public MemberRepository memberRepository() {
		return new MemoryMemberRepository();
	}
}
```

- 향후 메모리 레포지토리를 다른 레포지토리로 변경 예정이므로, 컴포넌트 스캔 방식 대신에 자바 코드로 스프링 빈을 설정

- **참고1 : DI 방식에는 아래와 같은 방식이 존재**
    - 필드 주입
    
    ```java
    @Controller
    public class HelloController {
    
    		@Autowired
        private MemberService memberService;
    
    }
    ```
    
    - setter 주입
    
    ```java
    @Controller
    public class HelloController {
    
        private MemberService memberService;
    
        @Autowired
        public setMemberService(MemberService memberService) {
            this.memberService = memberService;
        }
    }
    ```
    
    - 생성자 주입
    
    ```java
    @Controller
    public class HelloController {
    
        private final MemberService memberService;
    
        // Spring 4.3 이전 버전이라면 @Autowired 필요
        public HelloController(MemberService memberService) {
            this.memberService = memberService;
        }
    }
    ```
    
    의존 관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 **생성자 주입 권장**
    
    - 생성자 주입이 권장되는 이유
        - 순환 참조를 방지
        - 불변성 : 생성자 주입은 final로 등록 되므로, 한번 설정되면 변경될 수 없음
        - 테스트 코드 작성에 용이
    
    *참조링크* : [https://dev-coco.tistory.com/70](https://dev-coco.tistory.com/70)
    
- **참고2 :** 실무에서는 주로 정형화 된 컨트롤러, 서비스, 레포지토리 같은 컴포넌트 스캔을 사용.
    - 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 하는 경우에는 설정을 통해 스프링 빈으로 등록

- 주의 :  @Autowired를 통한 DI는 helloController, memberService와 같이 스프링이 관리하는 객체에서만 동작.
    
    ⇒ 스프링 빈으로 등록하지 않고 본인이 직접 생성한 객체에서는 동작하지 않음
