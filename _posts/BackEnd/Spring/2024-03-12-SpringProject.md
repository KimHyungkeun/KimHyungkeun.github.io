---
title: Spring Project 구조
date: 2024-03-12 00:00:00 +00:00
categories: [BackEnd, Spring]
tags:
  [
    BackEnd,
    Spring
  ]
---

# 1. IDE에서의 Spring 프로젝트 구조

- Spring Project 구조
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/a5579e14-af59-42ed-8185-88e64340cdc1/Untitled.png)
    
    - .gradle : gradle 작동에 있어 필요한 파일이 담김
    - .idea : intelliJ가 구동에 있어 필요한 파일이 담김
    - gradle : gradle build 후 결과가 담김
        - wrapper
    - src
        - main
            - java
            - resources
        - test
    - .gitignore
        - github에는 딱 필요한 소스코드 파일만 올라가야 함
        - 제외해야 할 것
            - 빌드한 결과물
            - 내 환경에서의 설정
    
- build.gradle 파일 보기
    - spring initializr 에서 미리 짜준 파
    - 선택한 정보들 build.gradle에 담겨 있다
    - spring boot : 3.2.3
    - java
    - group : zerobase
    - sourceCompatibility : 17
    - dependency들
        
        ```groovy
        plugins {
        	id 'java'
        	id 'org.springframework.boot' version '3.2.3'
        	id 'io.spring.dependency-management' version '1.1.4'
        }
        
        group = 'zerobase'
        version = '0.0.1-SNAPSHOT'
        
        java {
        	sourceCompatibility = '17'
        }
        
        configurations {
        	compileOnly {
        		extendsFrom annotationProcessor
        	}
        }
        
        repositories {
        	mavenCentral()
        }
        
        dependencies {
        	implementation 'org.springframework.boot:spring-boot-starter-web'
        	compileOnly 'org.projectlombok:lombok'
        	annotationProcessor 'org.projectlombok:lombok'
        	testImplementation 'org.springframework.boot:spring-boot-starter-test'
        }
        
        tasks.named('test') {
        	useJUnitPlatform()
        }
        
        ```
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/a8ee2833-99d9-4045-ac08-7739782159cc/Untitled.png)
        
        - implementation : 테스트 + 컴파일 + 실행에 모두 사용
        - compileOnly : 이 코드를 컴파일하는 그 시점에서만 사용한다는 의미
        - annotationProcessor
        - testImplementation : 테스트하는 시점에만 사용한다는 의미
    - dependency들은 어디서 다운 받는지 :
        - 기본적으로는 mavenCentral( )
        - 하지만, 사내에 따로 Repo가 있다면, 해당 Repo를 가리킬 것
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/acdc2d12-f948-4f76-88f3-77ecf85bcb7d/Untitled.png)
        
- JAR와 WAR 차이 :
    - JAR (**Java ARchive**)
        
        **1-1) 장점**
        
        - **독립적인 Java 애플리케이션을 패키징**할 수 있고, 실행 가능한 JAR 파일로 만들어서 개발자가 쉽게 애플리케이션을 실행할 수 있다.
        - 압축된 형식으로 클래스, 리소스, 라이브러리 등을 포함하므로 배포와 전달이 용이하다.
        - Java 애플리케이션의 진입점인 main 메서드를 포함하고 있어 **JVM에서 직접 실행**하기 때문에 별도의 **웹 컨테이너나 서버가 필요하지 않다.**
        
        **1-2) 단점**
        
        - 웹 애플리케이션을 개발할 때는 WAR 파일에 비해 제약사항이 있다. 예를 들어, JSP나 서블릿 컨테이너에 대한 표준 기능을 활용하기 어렵다.
        - 웹 애플리케이션에서 동적인 웹 컨텐츠 생성 및 관리가 제한적이다.
        
    - WAR (**Web ARchive)**
        
        **2-1) 장점**
        
        - Java 웹 애플리케이션을 패키징하는 데 최적화되어 있고, 웹 구성 요소인 JSP, 서블릿, 필터, 리스너 등과 웹 애플리케이션을 실행하기 위한 서블릿 컨테이너(웹 컨테이너)에서 필요한 설정 파일, 라이브러리, 리소스 등을 포함하여 **한 번에 배포 및 실행**할 수 있습니다.
        - 웹 컨테이너(서블릿 컨테이너)를 통해 애플리케이션을 실행하므로 **다양한 웹 기능과 서버 환경을 활용**할 수 있다.
        
        **2-2) 단점**
        
        - 웹 컨테이너나 서버에 **종속적이기 때문에 특정한 웹 컨테이너 환경이 필요**하다.
        - 배포 및 전달에 있어서 JAR 파일에 비해 크기가 크고 번거로울 수 있다.

## 2. 테스트 코드 실행하기

- TDD (Test Driven Development)
    - 테스트 주도 개발 : 테스트를 먼저 만들고 테스트를 통과하기 위한 코드를 짜는 것
        - 1) “날씨 데이터를 받아온다”는 테스트 코드 작성 (실패)
        - 2) 날씨 데이터를 받아오는 코드 작성
        - 3) “날씨 데이터를 받아온다”는 테스트 코드 실행 (실패)
        - 4) 날씨 데이터를 받아오는 코드 수정
        - 5) “날씨 데이터를 받아온다”는 테스트 코드 실행 (성공)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/d6e5b693-1dc6-43c7-a569-cadf422aeb3a/Untitled.png)
    

## 3. 테스트 코드 종류

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/7eed4d1a-2910-4500-af31-5b2b0950d064/Untitled.png)

- 예제)
```java
@SpringBootTest
@Transactional
public class JdbcMemoRepositoryTest {

    @Autowired
    JdbcMemoRepository jdbcMemoRepository;

    @Test
    void insertMemoTest() {

        //given
        Memo newMemo = new Memo(2, "insertMemoTest");

        //when
        jdbcMemoRepository.save(newMemo);

        //then
        Optional<Memo> result = jdbcMemoRepository.findById(2);
        assertEquals(result.get().getText(),"insertMemoTest");
    }

    @Test
    void findAllMemoTest() {
        List<Memo> memoList = jdbcMemoRepository.findAll();
        System.out.println(memoList);
        assertNotNull(memoList);
    }
}
```
