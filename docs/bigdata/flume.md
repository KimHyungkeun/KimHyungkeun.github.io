---
layout: default
title: Flume
parent: BigData
nav_order: 6
---

# Flume 설치 과정

Flume  : [https://earthconquest.tistory.com/231](https://earthconquest.tistory.com/231)
Java : [https://it-serial.tistory.com/entry/Linux-CentOS-7-Oracle-JAVA-설치-JDK-환경-변수-설정](https://it-serial.tistory.com/entry/Linux-CentOS-7-Oracle-JAVA-%EC%84%A4%EC%B9%98-JDK-%ED%99%98%EA%B2%BD-%EB%B3%80%EC%88%98-%EC%84%A4%EC%A0%95)

# 1. Java JDK 설치

## 1) 설치하려는 JDK Version 확인 (220524 기준 Java JDK 1.8)

```bash
$ yum list java *jdk-devel
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eb717657-d94a-4e37-a3d4-604e636c2dd2/Untitled.png)

## 2) JDK 설치

```bash
$ yum -y install java-1.8.0-openjdk-devel.x86_64
```

## 3) javac 위치 확인

```bash
$ which javac
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fc680ca7-f131-4f9c-95f5-41e88bebf2e2/Untitled.png)

## 4) readlink를 이용해 javac의 원본 위치 파악

```bash
$ readlink -f /usr/bin/javac
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/34602e8b-0d5c-43ee-9c28-88cf716dc3a9/Untitled.png)

## 5) 환경변수 설정

```bash
$ vi /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.332.b09-1.el7_9.x86_64/
$ source /etc/profile
```

## 6) 환경변수 확인

```bash
$ echo $JAVA_HOME
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.332.b09-1.el7_9.x86_64/
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/451bc607-c649-4aa9-b20d-9d850accbd2e/Untitled.png)

## 7) 설치 확인

```bash
$ java -version
openjdk version "1.8.0_332"
OpenJDK Runtime Environment (build 1.8.0_332-b09)
OpenJDK 64-Bit Server VM (build 25.332-b09, mixed mode)
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/79f918d1-4d91-4fda-8595-337f15136698/Untitled.png)

# 2. Flume 설치

## 1) Flume Download (220524 기준 Flume 1.9.0)

[https://flume.apache.org/download.html](https://flume.apache.org/download.html) 참고

```bash
$ mkdir /usr/local/application
$ cd /usr/local/application
$ wget **[https://dlcdn.apache.org/flume/1.9.0/apache-flume-1.9.0-bin.tar.gz](https://dlcdn.apache.org/flume/1.9.0/apache-flume-1.9.0-bin.tar.gz)**
$ tar -xzf apache-flume-1.9.0-bin.tar.gz
$ mv apache-flume-1.9.0-bin.tar.gz flume
```

## 2) 환경변수 설정

```bash
$ vi /etc/profile
```

```bash
# 맨 하단에 아래의 내용을 입력

export FLUME_HOME=/usr/local/application/flume
export PATH=$PATH:$FLUME_HOME/bin
export CLASSPATH=$CLASSPATH:$FLUME_HOME/lib/*.jar
```

```bash
$ source /etc/profile
```

## 3) 설치 확인

```bash
$ flume-ng version
```

```bash
Flume 1.9.0
Source code repository: https://git-wip-us.apache.org/repos/asf/flume.git
Revision: d4fcab4f501d41597bc616921329a4339f73585e
Compiled by fszabo on Mon Dec 17 20:45:25 CET 2018
From source with checksum 35db629a3bda49d23e9b3690c80737f9
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/203f5bad-7244-4575-ba5f-e45df6a66272/Untitled.png)

------------------------------------------------------------------------------------

# 1. 220524 이슈

- Windows 설치 진행 및 httpSource를 이용한 POST 진행 시도
설치 참고 : [https://heodolf.tistory.com/35](https://heodolf.tistory.com/35)
설치 참고2 : [https://han-py.tistory.com/366](https://han-py.tistory.com/366)
- agent.conf 파일을 만들어야 flume이 진행

```bash
# agent_dochi.conf

# Component 이름 지정

agent_dochi.sources = src
agent_dochi.channels = mem_chnl
agent_dochi.sinks = logger_sink

# Source 설정
agent_dochi.sources.src.type = http
agent_dochi.sources.src.bind = localhost
agent_dochi.sources.src.port = 81
agent_dochi.sources.src.handler = org.apache.flume.source.http.JSONHandler
agent_dochi.sources.src.channels = mem_chnl

# Channel 설정
agent_dochi.channels.mem_chnl.type = memory
agent_dochi.channels.mem_chnl.capacity = 1000

# Sink 설정
agent_dochi.sinks.logger_sink.channel = mem_chnl
agent_dochi.sinks.logger_sink.type = logger
agent_dochi.sinks.logger_sink.maxBytesToLog = 16
```

- 설치는 잘 진행 되었고, flume-ng 실행에 대해서는 bat파일을 만들어 진행

```bash
rem flume_start.bat

@echo off

pushd %~dp0
set FLUME_HOME=%cd%
popd

set JAVA_HOME=%FLUME_HOME%\java\jdk-11.0.15.1
set FLUME_PROPS=
set FLUME_PROPS=%FLUME_PROPS%;flume.root.logger=INFO,LOGFILE,console
set FLUME_PROPS=%FLUME_PROPS%;flume.log.dir=%FLUME_HOME%\logs
set FLUME_PROPS=%FLUME_PROPS%;flume.log.file=agent_dochi.log

%FLUME_HOME%\bin\flume-ng agent -name agent_dochi -conf %FLUME_HOME%\conf -conf-file %FLUME_HOME%\conf\agent_dochi.conf -property %FLUME_PROPS%
```

- 그러나, Windows에서 제대로 된 JAVA 경로를 가리킴에도 불구하고 제대로 된 작동이 되질 않음

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6e4da221-b41f-4341-919d-2503e8c1446d/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6fc6a212-40f3-4eff-a2db-ec9a3a1da40a/Untitled.png)

# 2. 220525 이슈

- WSL(CentOS 7) 기준으로는 구글링을 진행하며 셋팅 했던 결과, HttpSource 정상 작동 및 POST 이상 없이 작동

- 협의 결과, Docker를 이용해 flume container를 띄워서 진행해 보기로 함

```bash
version: "3.9"
services :
  nifi:
    container_name : nifi_for_test
    hostname: nifi_for_test
    image : apache/nifi:latest
    ports : 
      - 21177:21177
    environment :
      - NIFI_WEB_HTTP_PORT=21177
      - TZ=Asia/Seoul
    volumes :
      - test_nifi_log:/opt/nifi/nifi-current/logs
      - test_nifi_state:/opt/nifi/nifi-current/state
      - test_nifi_db:/opt/nifi/nifi-current/database_repository
      - test_nifi_flow:/opt/nifi/nifi-current/flowfile_repository
      - test_nifi_content:/opt/nifi/nifi-current/content_repository
      - test_nifi_provenance:/opt/nifi/nifi-current/provenance_repository
      - test_nifi_conf:/opt/nifi/nifi-current/conf

  flume :
    container_name : flume_for_test
    hostname : flume_for_test
    image : probablyfine/flume:latest
    ports :
      - 81:81
    environment :
      - FLUME_AGENT_NAME=docker
      - TZ=Asia/Seoul
    volumes :
      - test_flume_conf:/opt/flume-config/flume.conf

volumes :
  test_nifi_log :
    external : true
  test_nifi_state :
    external : true
  test_nifi_db :
    external : true
  test_nifi_flow :
    external : true
  test_nifi_content :
    external : true
  test_nifi_provenance :
    external : true
  test_nifi_conf :
    external : true
  test_flume_conf :
    external : true
```

- agent_test.conf를 만들어 진행

```bash

# agent_test.conf
agent_test.sources = httpsource
agent_test.channels = mem_chnl
agent_test.sinks = logger_sink
 
# Source : httpsource
agent_test.sources.httpsource.type = http
agent_test.sources.httpsource.bind = flume_for_test
agent_test.sources.httpsource.port = 81
agent_test.sources.httpsource.channels = mem_chnl

# Channel : Memory
agent_test.channels.mem_chnl.type = memory
agent_test.channels.mem_chnl.capacity = 100 # default
agent_test.channels.mem_chnl.transactionCapacity = 100 # default

 
# Sink : logger_sink
agent_test.sinks.logger_sink.channel = mem_chnl
agent_test.sinks.logger_sink.type = logger
agent_test.sinks.logger_sink.maxBytesToLog = 16

# flume-ng agent \
# -c /opt/flume/conf \
# --conf-file /opt/flume-config/flume.conf/agent_test.conf \
# --name agent_test \
# -Dflume.root.logger=INFO,console
```

- Interceptor를 통해 Json raw데이터를 NGSI-LD 포맷으로 전환하고 싶었으나
”key” : {“key”:”value”} 와 같은 object 형태의 values는 invalid syntax로 판별하고 진행되지 않음.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d428cfb0-a3fb-4084-be4d-fbf0118579f8/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3206b3cd-f2fd-465d-a286-5a824fcbd658/Untitled.png)

정상작동 예시)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/67415e56-3683-479b-b71a-ff83f45307b1/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5483b87a-dce0-4514-aa50-7bd6cd3a0b3e/Untitled.png)

- Apache Flume 공식 홈페이지 내 JSON Handler

JSONHandler :
[https://flume.apache.org/releases/content/1.9.0/apidocs/index.html?org/apache/flume/source/http/JSONHandler.html](https://flume.apache.org/releases/content/1.9.0/apidocs/index.html?org/apache/flume/source/http/JSONHandler.html)
    
    JSONHandler Github :
    [https://github.com/apache/flume/blob/trunk/flume-ng-core/src/main/java/org/apache/flume/source/http/JSONHandler.java](https://github.com/apache/flume/blob/trunk/flume-ng-core/src/main/java/org/apache/flume/source/http/JSONHandler.java)
    
    JSONHandler 공식 설명 :
    [https://flume.apache.org/FlumeUserGuide.html#jsonhandler](https://flume.apache.org/FlumeUserGuide.html#jsonhandler)
    

1) 한 Event 단위 당, Header와 body로만 구성되도록 함

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/581524b9-00fa-4ae5-b1d3-6a4c52af9444/Untitled.png)

2) Header내의 데이터가 String, String의 key-value pair로만 받음

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3671b95c-7593-442e-9a26-f51af65b9b4b/Untitled.png)

확인결과 : headers와 body의 묶음이 하나의 Event 단위로 형성이 된다.

**headers :** 내부의 key-value의 value는 **문자열이나 숫자**만 가능하고 그 이외는 불가능하다
**body : 문자열이나 숫자**만 가능하고 그 이외의 타입이나 형태는 불가능하다

```
[{
  "headers": {
             "timestamp": "434324343",
             "host": "random_host.example.com"
             },
  "body": "random_body"
  },
  {
  "headers": {
             "namenode": "namenode.example.com",
             "datanode": "random_datanode.example.com"
             },
  "body": "really_random_body"
  }]
```

### * 그 외, 시도해본 interceptor

- **Static Interceptor :** Key-value 형태의 필드를  header에 새로 추가한다.
⇒ 그러나, String:String이나 String:숫자 같은 형식만 가능

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bac5ad86-0b04-45b8-b36f-a85339687471/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/434d43d4-145e-474d-9a69-f4edc420a080/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bcd0892d-58f9-449c-bd25-333405cdf62a/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/32899685-f3a0-4fa2-8907-9e91c0c66981/Untitled.png)

- **Remove header Intertceptor** 
⇒ header내에서 지우고 싶은 key-value 필드를 없앤다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c3265c4a-d167-4b88-8b02-499b60d00af0/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eb6d9969-8c2d-45ab-9e60-c2fa12ba2a68/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/08ec02bc-c628-4ad2-8eb7-8e2378aba811/Untitled.png)

# 3. 220526 이슈

- HttpSource는 들어온 데이터에 대해 핸들링 하는 Handler가 존재한다.

- JSONHandler : 들어온 event들에 대해 JSON 포맷으로 핸들링 한다.
- BlobHandler : BLOB(Binary Large Object)에 대한 파일들을 다룬다(PDF, JPG와 같은 포맷)
⇒ 이 중, JSON Handler를 이용해야 JSON형태로 들어오는 파일을 포맷팅 하기에 적절하다.

- NiFi를 receiver로 셋팅하고 ListenHTTP 프로세서를 돌려본 결과, 
위 Flume 이벤트 포맷에서 “body” 부분만이 content로 들어간다.
(Nifi 상의 flowfile attribute를 살펴보았으나 headers에 서술된 내용은 들어가 있지 않음)
⇒ 즉, 주요 내용은 Body에 들어가야 한다는 의미이므로, raw data포맷은 body에 삽입해야함

```bash
# agent_test.conf
agent_test.sources = httpsource
agent_test.channels = mem_chnl
agent_test.sinks = my_local
 
# Source : httpsource
agent_test.sources.httpsource.type = http
agent_test.sources.httpsource.bind = flume_for_test
agent_test.sources.httpsource.port = 81
agent_test.sources.httpsource.channels = mem_chnl

# Channel : Memory
agent_test.channels.mem_chnl.type = memory
agent_test.channels.mem_chnl.capacity = 100
agent_test.channels.mem_chnl.transactionCapacity = 100
agent_test.channels.mem_chnl.keep_alive = 3
 
# Sink : http_sink
agent_test.sinks.my_local.type = http
agent_test.sinks.my_local.endpoint = http://192.168.1.17:2020/contentListener
agent_test.sinks.my_local.connectTimeout = 5000
agent_test.sinks.my_local.requestTimeout = 5000
agent_test.sinks.my_local.channel = mem_chnl
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f9b1ff16-2938-4a1d-8360-f75a9ac06c57/Untitled.png)

- Postman에서 body 내용을 작성하고 Send를 누른 상태로, NiFi “ListenHTTP” 로 받아들이면,
Flume Agent가 실행된 상태에서 5초 단위로 body 내용을 보낸다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/602ac381-4516-4646-b4cd-58abbd58ae20/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/446ebb8c-4c11-4c96-9f3f-6a8e273ab8ff/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dcd93416-0c96-4a58-ad9a-ea3fd7b08f60/Untitled.png)

- agent 설정값 확인 결과, http sink에 connectTimeout 및 requestTimeout이 5000ms로 default값 설정되어 있음. 그래서, 1000이나 1로 주어보기도 했지만 여전히 변화 없음

**connectTimeout :** The socket connection timeout in milliseconds (소켓 연결 시간 초과)

**requestTimeout :** The maximum request processing time in milliseconds (요청 최대 시간)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6e4fc661-9553-453b-bf85-1545cee0826e/Untitled.png)

- agent_test.channels.mem_chnl.transactionCapacity = 2
아래와 같이 이벤트는 3개로 주면, 한번에 처리할 수 있는 이벤트 갯수 한도를 넘었다며 오류

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6692e398-6edf-4886-aa32-cd69bb4386e9/Untitled.png)

```bash
[
    {  
    "headers": {	
        "value": 0.01, 
        "urn": "urn:x-iot:smartsantander:u7jcfa:t351", 
        "timestamp": "2022-05-25T06:44:01.204609+02:00", 
        "phenomenon": "illuminance", 
        "uom": "lux"
        },
    "body" : 
        "{ 
            \"value\": 0.01, 
            \"urn\": \"urn:x-iot:smartsantander:u7jcfa:t351\", 
            \"location\": {
                \"type\": \"Point\", 
                \"coordinates\": [-3.80938, 43.4631] 
            }, 
            \"timestamp\": \"2022-05-25T06:44:01.204609+02:00\", 
            \"phenomenon\": \"illuminance\", 
            \"uom\": \"lux\"
            
        }"
    },
    {  
    "headers": {	
        "value": 0.02, 
        "urn": "urn:x-iot:smartsantander:u7jcfa:t351", 
        "timestamp": "2022-05-25T06:44:01.204609+02:00", 
        "phenomenon": "illuminance", 
        "uom": "lux"
        },
    "body" : 
        "{ 
            \"value\": 0.02, 
            \"urn\": \"urn:x-iot:smartsantander:u7jcfa:t351\", 
            \"location\": {
                \"type\": \"Point\", 
                \"coordinates\": [-3.80938, 43.4631] 
            }, 
            \"timestamp\": \"2022-05-25T06:44:01.204609+02:00\", 
            \"phenomenon\": \"illuminance\", 
            \"uom\": \"lux\"
            
        }"
    },
    {  
    "headers": {	
        "value": 0.03, 
        "urn": "urn:x-iot:smartsantander:u7jcfa:t351", 
        "timestamp": "2022-05-25T06:44:01.204609+02:00", 
        "phenomenon": "illuminance", 
        "uom": "lux"
        },
    "body" : 
        "{ 
            \"value\": 0.03, 
            \"urn\": \"urn:x-iot:smartsantander:u7jcfa:t351\", 
            \"location\": {
                \"type\": \"Point\", 
                \"coordinates\": [-3.80938, 43.4631] 
            }, 
            \"timestamp\": \"2022-05-25T06:44:01.204609+02:00\", 
            \"phenomenon\": \"illuminance\", 
            \"uom\": \"lux\"
            
        }"
    }   
   
]
```

- **agent_test.channels.mem_chnl.capacity** : 메모리 채널 내에 최대 담을 수 있는 이벤트 갯수

- **agent_test.channels.mem_chnl.transactionCapacity** : 한번에 처리할 수 있는 이벤트 갯수

- capacity가 transactionCapacity보다 작으면 에러 발생
(아예, source와 sink간의 연결이 거부된다)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/03cee429-e9fa-41c3-84db-589356f4cf38/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8152bd7f-d4f0-48f0-8cb6-df30abaa8166/Untitled.png)

# 4. 220527 이슈

- **HTTP Sink** : 이벤트를 n개 transfer해도, 큐에서 맨 앞의 이벤트 내용만 나타나는 현상.
(ex) event3 → event2 → event1 순서로 큐가 되어있을 시, 
PostMan으로 send를 해도 event1의 내용만 찍히게 됨.)

**원인 :** 
**agent_test.sinks.my_local.defaultBackoff** = true
**agent_test.sinks.my_local.defaultRollback** = true
 의 값들이 default으로 true로 셋팅되어 있었음.

**defaultBackoff** : HTTP status code를 받으면 잠시 대기 상태가 되었다가 다음 작업 시작
**defaultRollback :** HTTP status code를 받으면 내용을 이전 상황으로 rollback 시킴
(property의 true 상태일때만 로그가 찍히게 되어, true 상태에서 로그 확인) 
(HTTP Status 200은 정상적으로 받고 있는 상태임을 확인)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6e4fc661-9553-453b-bf85-1545cee0826e/Untitled.png)

**Case 1) (기본값으로 셋팅됨)**
agent_test.sinks.my_local.defaultBackoff = true
agent_test.sinks.my_local.defaultRollback = true

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bc553e2c-bded-4ec9-a576-ade3f6d006da/Untitled.png)

→ Postman으로 한번 Send하면, 5초 간격으로 **큐의 맨 앞의 내용만** **receiver에게 반복 송신**

**Case 2)**
agent_test.sinks.my_local.defaultBackoff = false
agent_test.sinks.my_local.defaultRollback = true

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/360d86e3-ccc8-47f5-819d-aba397efdd56/Untitled.png)

→ Postman으로 한번 Send하면, 큐에 담긴 event 3개를 **일괄 묶어서** **receiver에게 반복 송신**

**Case 3)**
agent_test.sinks.my_local.defaultBackoff = true
agent_test.sinks.my_local.defaultRollback = false
(프로세스 상에서 로그가 찍히진 않음)
→ Postman으로 한번 Send하면, 
큐에 담긴 event 3개를 순차적으로 5초 간격으로 **한 번씩 읽어서 receiver에 송신**

**Case 4)**

agent_test.sinks.my_local.defaultBackoff = false
agent_test.sinks.my_local.defaultRollback = false

(프로세스 상에서 로그가 찍히진 않음)

→ Postman으로 한번 Send하면, 큐에 담긴 event 3개를 **일괄 묶어서** **receiver에게 한번 송신**

- flume 단독 컨테이너 생성
(6 core, Memory = 16GB)

```bash
docker run -d \
--name flume_iso_test \
--hostname flume_iso_test \
--cpuset-cpus=0,1,2,3,4,5 -m=16g \
-e FLUME_AGENT_NAME=docker \
-e TZ=Asia/Seoul \
-p 81:81 \
-v test_flume_iso_conf:/opt/flume-config/flume.conf \
probablyfine/flume:latest
```

- Jmeter 실험 시 넣었던 body내용

```bash
[
    {  
    "headers": {	

        },
    "body" : 
        "{ 
            \"value\": 0.05, 
            \"urn\": \"urn:x-iot:smartsantander:u7jcfa:t351\", 
            \"location\": {
                \"type\": \"Point\", 
                \"coordinates\": [-3.80938, 43.4631] 
            }, 
            \"timestamp\": \"2022-05-25T06:44:01.204609+02:00\", 
            \"phenomenon\": \"illuminance\", 
            \"uom\": \"lux\"
            
        }"
    }
 
]
```

```bash
# Channel : Memory
agent_test.channels.mem_chnl.type = memory
agent_test.channels.mem_chnl.capacity = 100 # channel에 담을 수 있는 이벤트 최대 갯수
agent_test.channels.mem_chnl.transactionCapacity = 100 # 한번에 일괄 처리 가능한 이벤트의 최대 갯수
```

- Jmeter 기준 Thread 갯수
- 100 : 정상 flow
- 300 : 정상 flow
- 500 : java.lang.OutOfMemoryError: Java heap space 에러 발생

⇒ 원인 : Memory channel 설정 이슈

Capacity나 transactionCapacity에 1000이나 10000을 넣어도

WARN Invalid capacity, initializing 100 이라는 WARN 로그가 발생하고, 메모리당 최대 적재 이벤트 갯수가 100으로 자동 지정됨.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0ce75a63-30ed-4860-ac7d-5272e945f209/Untitled.png)

⇒ 해결 : memory channel property 값 배정에 대해서, 설명 주석을 전부 이전 라인으로 배치

**(Before)**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3c071e79-13fd-4951-a6c4-ee2ebee8aa4e/Untitled.png)

**(After)**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd5d4fc1-570f-4933-bdcc-be0023d8beb4/Untitled.png)

# 5. 220530 테스트

1) 테스트 환경

- NiFi, Flume 공통 : Core 5, Memory 8G, Docker Container 환경
- NiFi와 Flume은 컨테이너를 각각 따로 지정할 것 (docker-compose X)
- NiFi Container Heap Memory : 최소 및 최대 Heap을 8G (-Xms : 8G, -Xmx : 8G)
**(nifi conf의 bootstrap.conf 파일 확인)**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a1419862-653c-4a33-9391-4d30ca0e028f/Untitled.png)

```bash
docker run -d \
--name nifi-test \
--cpuset-cpus=0,1,2,3,4 -m=8g \
-e NIFI_WEB_HTTP_PORT=9090 \
-e TZ=Asia/Seoul \
-p 8443:9090 \
-p 21177:21177 \
-v nifi-volume-data:/data \
-v nifi-content_repository:/opt/nifi/nifi-current/content_repository \
-v nifi-flowfile_repository:/opt/nifi/nifi-current/flowfile_repository \
-v nifi-logs:/opt/nifi/nifi-current/logs \
-v nifi-volume-drivers:/opt/nifi/drivers \
-v nifi-conf:/opt/nifi/nifi-current/conf \
-v nifi-database_repository:/opt/nifi/nifi-current/database_repository \
-v nifi-provenance_repository:/opt/nifi/nifi-current/provenance_repository \
-v nifi-state:/opt/nifi/nifi-current/state \
apache/nifi
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c32019ab-0d32-453e-99fa-805eccde2cf0/Untitled.png)

- Flume Container Heap Memory : 최소 및 최대 Heap을 8G (-Xms : 8G, -Xmx : 8G)
**(flume conf의 flume-env.sh의 JAVA_OPTS 환경 확인)**

```bash
docker run -d \
--name flume-test \
--hostname flume_iso_test \
--cpuset-cpus=0,1,2,3,4 -m=8g \
-e FLUME_AGENT_NAME=docker \
-e TZ=Asia/Seoul \
-p 81:81 \
-v test_flume_iso_conf:/opt/flume-config/flume.conf \
-v flume_conf:/opt/flume/conf \
probablyfine/flume:latest
```

```bash
# flume-env.sh

# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# If this file is placed at FLUME_CONF_DIR/flume-env.sh, it will be sourced
# during Flume startup.

# Enviroment variables can be set here.

# export JAVA_HOME=/usr/lib/jvm/java-8-oracle

# Give Flume more memory and pre-allocate, enable remote monitoring via JMX
# -Xss : 개별 쓰레드 별 최대 스택 크기
# -XX:MaxDirectMemorySize : 모든 직접 바이트 버퍼 크기의 합계는 이 한계를 초과할 수 없음
export JAVA_OPTS="-Xms8000m -Xmx8000m -Xss1024k -XX:MaxDirectMemorySize=256m"

# Let Flume write raw event data and configuration information to its log files for debugging
# purposes. Enabling these flags is not recommended in production,
# as it may result in logging sensitive user information or encryption secrets.
# export JAVA_OPTS="$JAVA_OPTS -Dorg.apache.flume.log.rawdata=true -Dorg.apache.flume.log.printconfig=true "

# Note that the Flume conf directory is always included in the classpath.
#FLUME_CLASSPATH=""
```

```bash
# agent_test.conf
agent_test.sources = httpsource
agent_test.channels = mem_chnl
agent_test.sinks = httpsink

# Source : httpsource
agent_test.sources.httpsource.type = http
agent_test.sources.httpsource.port = 81
agent_test.sources.httpsource.channels = mem_chnl

# Channel : Memory
# capacity : channel에 담을 수 있는 이벤트 최대 갯수
# transactionCapacity : 한번에 일괄 처리 가능한 이벤트 최대 갯수
agent_test.channels.mem_chnl.type = memory
agent_test.channels.mem_chnl.capacity = 100000
agent_test.channels.mem_chnl.transactionCapacity = 100

# Sink : httpsink
# defaultBackoff : channel에서 이벤트 하나를 정상수신 후 일정 시간 동안 wait하는 행위
# defaultRollback : channel에서 이벤트 하나를 정상수신 후 다시 rollback하는 행위
agent_test.sinks.httpsink.type = http
agent_test.sinks.httpsink.endpoint = http://{receiver_address}:18457/test/plz
agent_test.sinks.httpsink.channel = mem_chnl 
agent_test.sinks.httpsink.contentTypeHeader = application/json
agent_test.sinks.httpsink.acceptHeader = application/json
agent_test.sinks.httpsink.defaultBackoff = false 
agent_test.sinks.httpsink.defaultRollback = false 

# Flume Command
# flume-ng agent \
# --conf /opt/flume/conf \
# --conf-file /opt/flume-config/flume.conf/agent_test.conf \
# --name agent_test \
# -Dflume.root.logger=INFO,console
```

2) NiFi 테스트 **(Thread는 JMeter 에서 전송 시 설정하는 Thread 갯수)**

- **Case1)** **JMeter → Spring** 
(순수 HTTP Post를 전송 했을 때의 성능)
**- Thread 200 : TPS 34,000 / CPU 100%**
- **Case2)** **JMeter → ListenHttp(NiFi) → UpdateAttribute(NiFi) → InvokeHttp(NiFi) → Spring**
**- Thread 5 : TPS 2100 / CPU 100% (Queue X)
- Thread 6 : TPS 2200 / CPU 100% (Queue X)
- Thread 7 : TPS 2100 / CPU 100%
- Thread 10 : TPS 1700 / CPU 100%
- Thread 50 : TPS 670 / CPU 100%
- Thread 100 : TPS 210 / CPU 100%**
- **Case3)** **UpdateAttribute(NiFi) → InvokeHttp(NiFi) → Spring** 
(ListenHTTP 정지 상태 임에도 Jmeter 동작 시, CPU 100%를 사용했기 때문)
****방법 : 
1) ListenHTTP를 작동하여 UpdateAttribute사이에 Queue를 누적 시킨다
2) ListenHTTP를 정지 시키고, UpdateAttribute와 InvokeHttp사이의 flow를 확인
**- Result : TPS 1600 / CPU 70 ~ 100%**
- **Case4)** **InvokeHttp(NiFi) → Spring**
(Case3 에서 CPU 100% 사용 되고 있음. 따라서, NiFi Processor를 하나 제거 후 CPU 비교 테스트)
**- Result : TPS 1700 / CPU 60 ~ 100%**

3) 이슈 : NiFi

- ListenHTTP가 CPU Thread 사용량의 대부분을 차지
- InvokeHttp Connection Time out → 1 sec (길어질수록 느려진다)
- InvokeHttp Connection Pool → 많아지면 느려진다
- InvokeHttp Thread 설정

4) Flume 테스트

- Thread 1 : TPS 500 / CPU 60% 

- Thread 5 : TPS 800 / CPU 70%

- Thread 6 : TPS 700 / CPU 70%

⇒ **CPU를 100% 모두 쓰지 않는 이유**
코드 분석 결과, HttpSink에는 HTTP Connect에 대해 Multi Thread 지원을 하지 않는다.
(runnable, thread class가 존재하지 않음)
→ 따라서, connection을 한정적으로 수행 ⇒ (TPS일정, LPU 사용량 일정)

[https://github.com/apache/flume/blob/trunk/flume-ng-sinks/flume-http-sink/src/main/java/org/apache/flume/sink/http/HttpSink.java](https://github.com/apache/flume/blob/trunk/flume-ng-sinks/flume-http-sink/src/main/java/org/apache/flume/sink/http/HttpSink.java)