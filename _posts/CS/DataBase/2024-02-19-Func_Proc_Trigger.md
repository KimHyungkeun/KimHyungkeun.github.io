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

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/6ef65582-43e5-4948-a5ba-0c312025caed)


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

![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/285a321e-451a-4c66-a453-c5e710b17280)


## 2. 프로시저

- 여러 개의 SQL문을 같이 실행 (같이 실행이라기 보다는 비즈니스 업무 로직을 수행)
- 네트워크를 통한 쿼리의 전송 속도가 빠르다고 함
- DB에서 바로 수정 적용이 가능한 장점 있음
- DB와 서버 개발을 구분해서 개발 가능 (사실, DBA가 해야할 일)

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/f94334f4-7a5a-4788-a2da-d09ef60ad2b5)


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

![Untitled 3](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/c266d0cf-8d56-4ba2-9fb8-91573082cc68)


## 3. 트리거 (Trigger)

- 특정 조건이 만족하면 저절로 실행되는 일종의 장치
- 프로시저(Procedure)나 함수(function)과 다르게 한번 설정을 하면 동작을 항상 감시하고 있다가 조건에 해당하는 동작이 수행되는 순간 실행되는 특징
- 주로, INSERT, UPDATE, DELETE 동작에 사용
- 동작 시점은 After, Before가 있음

![Untitled 4](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/eab0ad48-1145-4fbe-8ae8-b1c1e5a203c2)


![Untitled 5](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/a271a730-fd3d-4409-bb0c-af0dc8042112)


![Untitled 6](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/4df2812a-ef37-4b34-97f6-1e114c247c8e)


### (예제)  아래 테이블에서 내용이 바뀌면 history 테이블에 내용이 들어가도록 진행

- member_detail 테이블

![Untitled 7](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/5226b36e-340b-411b-b1e8-9736201e27fe)


- member_detail_history 테이블

![Untitled 8](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/71b66f32-0f90-4c12-b8b6-90d6f76987f4)


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

![Untitled 9](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/f56702c2-6ed3-4b47-afa0-881400fd8b46)


- **과정3** : member_detail_history에서 이전 변경내용 확인

![Untitled 10](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/80d80949-23bf-4e78-8f61-7969ae0b4cb1)
