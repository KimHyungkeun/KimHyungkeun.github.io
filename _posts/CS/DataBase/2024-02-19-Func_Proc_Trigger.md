---
title: 함수, 트리거, 프로시저
date: 2024-02-19 00:00:00 +00:00
categories: [CS, Database]
tags:
  [
    CS,
    Database
  ]
---

## 1. 함수

- DBMS에서 제공되는 공통적 함수 이외에 사용자가 직접 정의하고 작성
- SQL을 활용하여 일련의 로직을 수행하고, 수행 결과를 단일 값으로 반환할 수 있는 모듈

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/b76a4f2a-dce5-42fd-ab97-db87d52b8b7f/Untitled.png)

```sql
-- passwords를 입력하면 해당 결과를 '*****'로 반환
delimiter $$
create function sf_password (passwords varchar(255)) 
	returns varchar(255)
begin
	return '*****';
end;

select sf_password('1234'); 
drop function sf_password;
delimiter;
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/3a706614-6e7f-4213-a00d-c3c02a733035/Untitled.png)

## 2. 프로시저

- 여러 개의 SQL문을 같이 실행 (같이 실행이라기 보다는 비즈니스 업무 로직을 수행)
- 네트워크를 통한 쿼리의 전송 속도가 빠르다고 함
- DB에서 바로 수정 적용이 가능한 장점 있음
- DB와 서버 개발을 구분해서 개발 가능 (사실, DBA가 해야할 일)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/c92e5aba-9b48-4bef-9bd8-5813319ff0e4/Untitled.png)

```sql
delimiter $$
create procedure sp_select_member()
begin
	select * from mytable limit 2 offset 2;
end;

call sp_select_member();
drop procedure sp_select_member;
delimiter;
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/9d9777c1-856a-4a7c-9302-7cbcedd813ae/Untitled.png)

## 3. 트리거 (Trigger)

- 특정 조건이 만족하면 저절로 실행되는 일종의 장치
- 프로시저(Procedure)나 함수(function)과 다르게 한번 설정을 하면 동작을 항상 감시하고 있다가 조건에 해당하는 동작이 수행되는 순간 실행되는 특징
- 주로, INSERT, UPDATE, DELETE 동작에 사용
- 동작 시점은 After, Before가 있음

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/94b278b2-3ecd-4def-b050-794f5d5fca48/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/afef4208-8ce6-4e44-8793-f66cb4d00de0/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/b045cba8-6106-479a-bc9b-d577a4ed5c2f/Untitled.png)

### (예제)  아래 테이블에서 내용이 바뀌면 history 테이블에 내용이 들어가도록 진행

- member_detail 테이블

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/5467c5d7-cffa-4f98-a529-cc9758fae539/Untitled.png)

- member_detail_history 테이블

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/372c371c-0b49-4fd1-bfbb-ce50aa53c8b4/Untitled.png)

- **사전 과정** : trigger 생성

```sql
delimiter $$
create trigger tg_member_mobile_no_history
	before update on member_detail
    for each row
    begin
		insert into member_detail_history
			(member_type, user_id, mobile_no, update_date)
		values 
			(old.member_type, old.user_id, old.mobile_no, now());
    end;
delimiter;
```

- **과정1** : 아래와 같이 member_detail row 하나를 update

```sql
update member_detail
set
	mobile_no = '01055555555'
where member_type = 'email'
	and user_id = 'test@naver.c0m'
```

- **과정2** : member_detail 변경내용 확인

```sql
select * from member_detail;
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/502e3498-f1b2-466b-b019-990198378232/Untitled.png)

- **과정3** : member_detail_history에서 이전 변경내용 확인

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/41ff565a-df46-4042-a224-571918618881/Untitled.png)