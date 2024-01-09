---
title: NiFi Scenario 
date: YYYY-MM-DD HH:MM:SS +00:00
categories: [DataEngineering, NiFi]
tags:
  [
    DataEngineering,
    NiFi
  ]
---

# Nifi_Scenario(2022/5/23)

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

![Nifi_flow_picture](https://user-images.githubusercontent.com/12759500/229808770-b049da81-c415-4150-b46b-5ca6fb8332a9.jpg)

# 3. Processor 모음

## (1) ExecuteProcess - Shell Script 작동부분

![Untitled](https://user-images.githubusercontent.com/12759500/229808833-65100a61-6bc1-43c7-905b-bb75c7a87e6c.png)

![Untitled 1](https://user-images.githubusercontent.com/12759500/229808865-e691200c-a7fc-4e29-8ad8-50c7a6b8d3d9.png)

## (2) Data 적재 파이프라인 부분

**1) GetSFTP**

- ssh을 이용해 원격 서버에 접속하여 데이터를 가져온다

![Untitled 2](https://user-images.githubusercontent.com/12759500/229808883-c76d9dbb-2b9e-4e25-b8ca-fce7b87cfe3f.png)

![Untitled 3](https://user-images.githubusercontent.com/12759500/229808909-e24b9d1a-9da9-46b1-8bb5-4ad9fb9a2f42.png)

![Untitled 4](https://user-images.githubusercontent.com/12759500/229808923-c08b6196-4c95-4ea1-8f7c-01c6f33e0b16.png)

**2) OnlyTodayCSV (RouteOnAtrribute)**

- 불러온 데이터 중에 금일 날짜의 데이터만 불러온다.
- ex) 오늘이 2022-05-23이라면 20220523.csv만 불러온다

![Untitled 5](https://user-images.githubusercontent.com/12759500/229808962-6a898524-117d-47b0-b8bc-030b21adb464.png)

![Untitled 6](https://user-images.githubusercontent.com/12759500/229808993-acfb3ee3-c186-47ac-b11a-9063db34773c.png)

**3) AddValidSchema (UpdateAttribute)**

- 이번 시나리오에서 진행할 사용자 지정 스키마 속성을 추가한다

![Untitled 7](https://user-images.githubusercontent.com/12759500/229809023-3c77794c-9788-47aa-9170-e6e4bcc686e8.png)

![Untitled 8](https://user-images.githubusercontent.com/12759500/229809043-74bc204f-7778-4f5b-b97b-f3ad2d1112a4.png)

**4) ValidateRecord**

- 들어온 데이터가 사용자 지정 스키마(hkschema)를 기준으로 부합한지 확인한다.

![Untitled 9](https://user-images.githubusercontent.com/12759500/229809062-a3bfe69c-5640-4081-9137-999820f2be00.png)

**5)-1 Valid**

**(1)** **UpdateRecord**

- 기존의 csv는 4개의 컬럼밖에 없으므로, filename이라는 새 컬럼을 추가한다.

![Untitled 10](https://user-images.githubusercontent.com/12759500/229809081-4060e9dd-5856-4165-b33d-32456e0749a5.png)

![Untitled 11](https://user-images.githubusercontent.com/12759500/229809098-e9191c02-423f-4255-9863-1586e8ed1309.png)

**(2)** **ConvertJSONToSQL_valid**

- JSON형태의 데이터 내에서, row별로 insert sql 구문으로 변형시킨다

![Untitled 12](https://user-images.githubusercontent.com/12759500/229809126-fc112274-44af-438d-92c0-39e7bce37544.png)

![Untitled 13](https://user-images.githubusercontent.com/12759500/229809141-7fbbb331-9618-457e-9ae5-0b12ab4bfc75.png)

**(3)** **Attribute_InsertSQL_valid (UpdateAttribute)**

- 실제 실행될 Insert 구문내용을 담은 attribute를 추가한다

![Untitled 14](https://user-images.githubusercontent.com/12759500/229809187-8dbe04ce-4686-401f-91db-2b1036a8a62b.png)

![Untitled 15](https://user-images.githubusercontent.com/12759500/229809208-fa4e529f-c30a-4d6b-8b26-e33175b7eebd.png)

**5)-2 Invalid**

**(1)** **AddInvalidSchema**

- invalid한 스키마타입에 대해 정의하고 추가한다. 
(유효하지 않은 데이터에 대해서는 일괄 String 타입으로 처리)

![Untitled 16](https://user-images.githubusercontent.com/12759500/229809246-291390b6-63de-4f4a-82c2-7d3d335628ab.png)

**(2)** **UpdateRecord**

- 기존의 csv는 4개의 컬럼밖에 없으므로, filename이라는 새 컬럼을 추가한다.

![Untitled 17](https://user-images.githubusercontent.com/12759500/229809268-2845a3e6-5981-410f-bbb6-ee0773a7e068.png)

**(3)** **ConvertJSONToSQL_invalid**

- JSON형태의 데이터 내에서, row별로 insert sql 구문으로 변형시킨다

![Untitled 18](https://user-images.githubusercontent.com/12759500/229809298-7990bb11-4ca2-42e8-a34c-762b5aac61a9.png)

![Untitled 19](https://user-images.githubusercontent.com/12759500/229809315-84d2c9ba-de5a-4ac2-bae4-f62fbdfd3e67.png)

**(4)** **Attribute_InsertSQL_invalid (UpdateAttribute)**

- 실제 실행될 Insert 구문내용을 담은 attribute를 추가한다

![Untitled 20](https://user-images.githubusercontent.com/12759500/229809337-ce6091b4-0256-4a20-8d25-9f5d7f978cab.png)

**6) InsertDataAndLog (ExecuteSQL)**

- SQL문을 삽입하고, 삽입과 동시에 진행내용을 담은 log를 적재한다

![Untitled 21](https://user-images.githubusercontent.com/12759500/229809347-a0c647a8-1778-432b-a1bd-0c0a4666c077.png)

![Untitled 22](https://user-images.githubusercontent.com/12759500/229809365-45b9e5e7-0c28-4373-9965-b7b229b0139b.png)

# 4. NiFi Flow Configuration 설정

**(1) cattleAvroSchemaRegistry : valid 스키마 설정**

![Untitled 23](https://user-images.githubusercontent.com/12759500/229809394-6b89b184-9858-4ddf-aa91-17e98cbaaf3b.png)

- **cattleCSVReader : csv형태의 파일 읽기**
    
    ![Untitled 24](https://user-images.githubusercontent.com/12759500/229809408-22a353c7-8e7c-45b5-88f1-9583189e4dc0.png)
    
- **cattleCSVRecordSetWriter : csv형태로 파일 쓰기**
    
    ![Untitled 25](https://user-images.githubusercontent.com/12759500/229809438-99f82230-0001-4786-8f5d-27b69f307dbb.png)
    
- **cattleJsonRecordSetWriter : Json형태로 파일 쓰기**
    
    ![Untitled 26](https://user-images.githubusercontent.com/12759500/229809455-e5d3a9f8-9631-41a6-bad2-1d9c1b22b935.png)
    

**(2) nocattleAvroSchemaRegestry : invalid 스키마 설정**

![Untitled 27](https://user-images.githubusercontent.com/12759500/229809481-9a47df2d-1d2a-49e7-950e-c0995509a581.png)

- **nocattleCSVReader : csv형태로 파일 읽기**
    
    ![Untitled 28](https://user-images.githubusercontent.com/12759500/229809510-e18dba04-eb11-4155-9f3b-d46c7e5797de.png)
    
- **nocattleJSONRecordSetWriter : Json형태로 파일 쓰기**
    
    ![Untitled 29](https://user-images.githubusercontent.com/12759500/229809555-eb90d44c-7cc1-4bb1-a5e3-c52483e84e59.png)
    

**(3) cattleDBCPConnectionPool : PostgreSQL 데이터 연결 커넥션 풀**

![Untitled 30](https://user-images.githubusercontent.com/12759500/229809583-5088dfee-ba0d-472a-8525-0bfef90d9dcf.png)

