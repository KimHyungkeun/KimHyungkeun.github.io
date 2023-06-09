---
layout: default
title: 7. AOP
nav_order: 7
parent: Spring Lecture
grand_parent: Spring
permalink: /docs/spring/spring_lecture/spring_aop
---

# 7. AOP

## 1. AOP가 필요한 상황

- MemberService 회원 조회 시간 측정

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import javax.transaction.Transactional;
import java.util.List;
import java.util.Optional;

@Transactional
public class MemberService {

    private final MemberRepository memberRepository;

    @Autowired
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    /**
     * 
     회원 가입
     */
    public Long join(Member member) {

        long start = System.currentTimeMillis();

        try {
            // 같은 이름이 있는 중복 회원 X
            validateDuplicateMember(member);
            memberRepository.save(member);
            return member.getId();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join = " + timeMs + "ms");
        }

    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m ->
                {
                    throw new IllegalStateException("이미 존재하는 회원입니다.");
                });
    }

    /**
     * 
     * 전체 회원 조회
     */
    public List<Member> findMembers() {
        long start = System.currentTimeMillis();
        try {
            return memberRepository.findAll();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("findMembers " + timeMs + "ms");
        }
    }

    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```

- 문제점
    - 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
    - 시간을 측정하는 로직은 공통 관심 사항이다.
    - 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
    - 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
    - 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.

## 2. AOP 적용

- AOP: Aspect Oriented Programming
- 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리

![Untitled](7%20AOP%20(20221204%20~%2020221205)%20a4410ebefd094c51902b0fad93365e6c/Untitled.png)

- AOP작성 (TimeTraceAop)

```java
package hello.hellospring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("START: " + joinPoint.toString());
        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");
        }

    }
}
```

- 해결
    - 회원가입, 회원 조회등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다.
    - 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.
    - 핵심 관심 사항을 깔끔하게 유지할 수 있다.
    - 변경이 필요하면 이 로직만 변경하면 된다.
    - 원하는 적용 대상을 선택할 수 있다.

![Untitled](7%20AOP%20(20221204%20~%2020221205)%20a4410ebefd094c51902b0fad93365e6c/Untitled%201.png)

![Untitled](7%20AOP%20(20221204%20~%2020221205)%20a4410ebefd094c51902b0fad93365e6c/Untitled%202.png)

- 실제 Proxy가 주입되는지 콘솔에 출력해서 확인하기

![Untitled](7%20AOP%20(20221204%20~%2020221205)%20a4410ebefd094c51902b0fad93365e6c/Untitled%203.png)