---
layout: default
title: NiFi Scenario
parent: Ni-Fi
nav_order: 3
permalink: /docs/bigdata/nifi/scenario
---

# Nifi_Scenario(5/23)

# 1. 목표

- 특정 시간에 생성되는 CSV 데이터를 시나리오가 요구하는 DB에 적합한 형태로 저장
- 특정 디렉토리에 csv 파일 생성 확인 및 읽기
    1. CSV 데이터 Validation
    2. 데이터 값의 Valid / Invalid 여부로 분기 처리
    3-1) Valid한 값 cattle_auction_data 테이블에 저장 
    **(A:문자열, B:문자열, C:문자열, D:숫자, E(파일명):문자열)**
    3-2) Invalid한 값 cattle_auction_invalid_data 테이블에 저장
    **(A:문자열, B:문자열, C:문자열, D:문자열, E(파일명):문자열)**
    3. 실행 Log 저장 (validation_log 테이블)
    4. 완성된 Workflow Template으로 저장.

# 2. 전체적인 파이프라인 흐름

![Nifi_flow_picture.jpg](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Nifi_flow_picture.jpg)

# 3. Processor 모음

## (1) ExecuteProcess - Shell Script 작동부분

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%201.png)

## (2) Data 적재 파이프라인 부분

**1) GetSFTP**

- ssh을 이용해 원격 서버에 접속하여 데이터를 가져온다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%202.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%203.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%204.png)

**2) OnlyTodayCSV (RouteOnAtrribute)**

- 불러온 데이터 중에 금일 날짜의 데이터만 불러온다.
- ex) 오늘이 2022-05-23이라면 20220523.csv만 불러온다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%205.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%206.png)

**3) AddValidSchema (UpdateAttribute)**

- 이번 시나리오에서 진행할 사용자 지정 스키마 속성을 추가한다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%207.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%208.png)

**4) ValidateRecord**

- 들어온 데이터가 사용자 지정 스키마(hkschema)를 기준으로 부합한지 확인한다.

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%209.png)

**5)-1 Valid**

**(1)** **UpdateRecord**

- 기존의 csv는 4개의 컬럼밖에 없으므로, filename이라는 새 컬럼을 추가한다.

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2010.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2011.png)

**(2)** **ConvertJSONToSQL_valid**

- JSON형태의 데이터 내에서, row별로 insert sql 구문으로 변형시킨다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2012.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2013.png)

**(3)** **Attribute_InsertSQL_valid (UpdateAttribute)**

- 실제 실행될 Insert 구문내용을 담은 attribute를 추가한다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2014.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2015.png)

**5)-2 Invalid**

**(1)** **AddInvalidSchema**

- invalid한 스키마타입에 대해 정의하고 추가한다. 
(유효하지 않은 데이터에 대해서는 일괄 String 타입으로 처리)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2016.png)

**(2)** **UpdateRecord**

- 기존의 csv는 4개의 컬럼밖에 없으므로, filename이라는 새 컬럼을 추가한다.

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2017.png)

**(3)** **ConvertJSONToSQL_invalid**

- JSON형태의 데이터 내에서, row별로 insert sql 구문으로 변형시킨다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2018.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2019.png)

**(4)** **Attribute_InsertSQL_invalid (UpdateAttribute)**

- 실제 실행될 Insert 구문내용을 담은 attribute를 추가한다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2020.png)

**6) InsertDataAndLog (ExecuteSQL)**

- SQL문을 삽입하고, 삽입과 동시에 진행내용을 담은 log를 적재한다

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2021.png)

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2022.png)

# 4. NiFi Flow Configuration 설정

**(1) cattleAvroSchemaRegistry : valid 스키마 설정**

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2023.png)

- **cattleCSVReader : csv형태의 파일 읽기**
    
    ![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2024.png)
    
- **cattleCSVRecordSetWriter : csv형태로 파일 쓰기**
    
    ![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2025.png)
    
- **cattleJsonRecordSetWriter : Json형태로 파일 쓰기**
    
    ![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2026.png)
    

**(2) nocattleAvroSchemaRegestry : invalid 스키마 설정**

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2027.png)

- **nocattleCSVReader : csv형태로 파일 읽기**
    
    ![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2028.png)
    
- **nocattleJSONRecordSetWriter : Json형태로 파일 쓰기**
    
    ![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2029.png)
    

**(3) cattleDBCPConnectionPool : PostgreSQL 데이터 연결 커넥션 풀**

![Untitled](Nifi_Scenario(5%2023)%20f741e02a3e5d4eb98346670c13e511a0/Untitled%2030.png)

