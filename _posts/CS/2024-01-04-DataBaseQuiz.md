---
title: Database 퀴즈
date: YYYY-MM-DD HH:MM:SS +00:00
categories: [CS, Database]
tags:
  [
    CS,
    DataBase
  ]
---

# DataBase 퀴즈

**출처:** [https://csbroker.io/problem?page=1](https://csbroker.io/problem?page=1) (카테고리 : 데이터베이스)


---
**1. ACID**  
- **RDBMS 트랜잭션의 원칙**  

  **1) 원자성 (Atomacity)** : 하나의 트랜잭션은 분할이 되지 않기 때문에 전부 수행되거나 전부 수행되지 않아야한다.
  **2) 일관성 (Consistency)** : 트랜잭션이 실행을 성공적으로 완료하면 언제나 일관성 있는 데이터베이스 상태로 유지해야 한다.
  **3) 격리성 (Isolation)** : 서로 다른 트랜잭션끼리는 의존성이 존재하면 안된다. 즉 서로의 트랜잭션에 관여되지 않아야 한다.
  **4) 영구성 (Durability)** : 한 번 적용된 트랜잭션의 내용들은 절대 유실되어서는 안된다

**2. NoSQL의 특징**
- 스키마가 없기 때문에 유연하며 자유로운 데이터 구조를 가짐
- 데이터 분산이 용이 => Scale-up과 Scale-out 둘 다 유연
- 키 값 중복이 발생할 수 있으므로 키 값 설계가 중요
- 종류 : Key-Value 방식, Document 방식, Graph 방식

**3. RDBMS의 특징**
- Key를 통해서 연관된 정보들으 함께 조회하는데 특화되어 있음
- ACID 트랜잭션을 보장
- 데이터 무결성 유지를 위해 NoSQL보다는 속도가 느림
- 종류 : Oracle, MySQL, PostgreSQL, MariaDB 등

**4. NoSQL과 RDBMS의 차이**
- NoSQL은 구조를 정해두지 않음 => 따라서 형식이 자유롭다
- NoSQL은 데이터베이스 확장이 필요하다면 데이터베이스를 추가하면 되므로 수평적 확장에 용이

**5. Super Key(슈퍼키)란?**
- 릴레이션을 구성하는 모든 튜플에 대해 유일성(Unique)은 만족하지만, 최소성(Minimality)은 만족하지 못한다.
  - 유일성 (O), 최소성 (X)

**6. Candidate Key(후보키)란?**
- 릴레이션을 구성하는 속성들 중에서 튜플을 유일하게 식별하기 위해 사용되는 속성들의 부분집합
- 기본키(Primary Key)로 사용할 수 있는 속성
유일성과 최소성을 모두 만족시켜야 한다.
  - 유일성 (O), 최소성 (O)

**7. Primary Key(기본키)란?**
- 후보키중 엔티티를 대표할 수 있는 키로써 각 행을 유일하게 구별할 수 있는 속성 보유 - 유일성, 최소성을 모두 만족하고 NULL값을 가져선 안된다.
  - 유일성 (O), 최소성 (O), NULL (X)

**8. 릴레이션 특징**
- 튜플들의 삽입, 삭제 등의 작업으로 인해 릴레이션은 시간에 따라 변한다.
- 한 릴레이션에 포함된 튜플들은 모두 상이하다.
- 애트리뷰트는 논리적으로 쪼갤 수 없는 원자값으로 저장한다.


**9. 인덱스와 Cardinality**
- 일반적으로 RDBMS는 Cardinality(카디널리티)와 Selectivity(선택도)를 고려하게 된다.
- Cardinality : 특정 데이터 집합의 유니크(Unique)한 값의 갯수
- Selectivity : 전체 레코드 중에서 조건절에 의해 선택될 것으로 예상되는 레코드의 비율(%) 
  - Selectivity = Cardinality / 총 레코드 수 * 100
- 이 때, **Cardinality가 높고** **Selectivity가 낮을수록** 인덱스 사용에 더 용이하다

---