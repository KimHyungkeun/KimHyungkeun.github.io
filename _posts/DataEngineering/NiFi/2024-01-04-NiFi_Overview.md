---
title: NiFi Overview 
date: 2024-01-04 00:00:00 +00:00
categories: [DataEngineering, NiFi]
tags:
  [
    DataEngineering,
    NiFi
  ]
---

# Apache Nifi Overview

### What is Nifi?

- Apache의 오픈소스로, 분산 환경에서 대량의 데이터를 수집, 처리 할 수 있는 소프트웨어이다.
- 나이파이(NiFi)는 소프트웨어 시스템 간 데이터 흐름을 자동화 하도록 설계되었다.
(실시간 ETL 개념이라고 생각하면 된다.)
- 시스템간 데이터 전달 (처리, 관리, 모니터링) 위한 서비스 → Hortonworks에서 기술지원 가능

# 1. Core Concepts of Nifi

- 구성요소

- FlowFile : NiFi가 인식하는 데이터 단위. Attribute와 content로 구성되어 있다.
1) Attribute는 데이터에 대해 알려진 내용과 데이터를 Key-Value 형태로 저장
2) Content는 데이터의 실제 내용을 반영

![Untitled](https://user-images.githubusercontent.com/12759500/229808426-9fbe689b-dd5b-4972-bfc5-261a1a69075b.png)

![Untitled 1](https://user-images.githubusercontent.com/12759500/229808455-f61b7ee7-b078-4f64-8259-3ac988c8a0cb.png)

“i” : 클릭 → Attribute 탭에서 내용 확인 가능
”eye icon” : 파일에 담긴 content(내용) 확인 (아무것도 적혀있지 않으면 확인 불가)

- FlowFile Processor : FlowFile을 수집, 변형, 저장하는 기능

- Connection : Processor와 Processor를 연결하여 FlowFile을 전달한다.

- Flow Controller : Processor가 어느 간격 또는 시점에 실행하는지 스케쥴링

- Process Group : Processor에 대해서 일정 파이프라인을 하나의 그룹으로 묶어서 사용 가능하다.

# 2. Apache Nifi Architecture

![Untitled 2](https://user-images.githubusercontent.com/12759500/229808480-2dbf14f3-2c9e-4327-89fe-9144f686424a.png)

- NiFi는 JVM 환경에서 실행되며, 위 그림과 같은 컴포넌트 들로 구성되어 있다.

1) WebServer : 
- NiFi는 웹 서비스를 통해 제공하며, 개발자 혹은 관리자는 이를 이용하여 Dataflow 개발, 제어, 모니터링을 한다

2) Flow Controller 
- Processor들의 실제 스케쥴링을 담당한다.

3) Extensions 
- NiFi가 제공하는 기본 Processor들 외, 개발자가 프로세스를 개발해 확장 가능하다.

4) FlowFile Repository 
- FlowFile의 상태와 속성 값들을 저장하는 곳(메타데이터)이다.  시스템 장애 때 유실되지 않게 한다. 

5) Content Repository 
- FlowFile의 데이터가 저장되는 곳이다. 여러 디렉토리에 분석 저장이 가능하다.

6) Provenance Repository 
- 데이터의 처리 단계 별로 FlowFile 변화 데이터를 보관하는 곳(History data)으로,  
각 데이터는 인덱스 되어 검색할 수 있다.

# 3. Brief overview on Key Features

- NiFi는 시스템 간 데이터 흐름을 효율적으로 처리, 관리, 모니터링 하기 위한 최적의 시스템 이다.
- 주요 기능은 크게 FlowFile, Processor들로 나뉜다.
- FlowFile은 속성(Attribute)과 내용(Contents)로 나뉜다.
- Processor들에 대해서, 특정 역할을 수행하거나 묶고 싶을 경우, 그룹화가 가능하다.
이를 Processor Group이라고 한다.

참고 : [https://nifi.apache.org/docs.html](https://nifi.apache.org/docs.html)

참고 : [https://dahye-jeong.gitbook.io/til/database/2020-02-02-basic/2020-03-27-nifi](https://dahye-jeong.gitbook.io/til/database/2020-02-02-basic/2020-03-27-nifi)
