---
title: Network Summary
date: 2024-01-24 00:00:00 +00:00
categories: [CS, Network]
tags:
  [
    CS,
    Network
  ]
---

# 1.**OSI 7계층**

**(Open System Interconnection Reference Model)**  

국제표준화기구 에서 개발한 모델. 통신 접속에서 완료까지의 과정을 7단계로 표현

![Untitled](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/49da073c-61b8-4ec9-9a63-50a102e35913)


## (1) 물리(Physical)

- 데이터를 전기적인 신호로 변환해서 주고받는 기능을 진행

(ex : 리피터, 케이블, 허브)

## (2) 데이터 링크(Data Link)

- 물리 계층으로 송수신되는 정보를 관리하여 안전하게 전달하도록 도움

- Mac 주소를 통해 통신

(ex : 스위치, 브릿지)

## (3) 네트워크(Network)

- 데이터를 목적지까지 가장 안전하고 빠르게 전달

- 라우터를 통해 이동할 경로를 선택해 IP주소를 지정하고, 경로따라 패킷 전달

(ex : 라우터, IP)

## (4) 전송(Transport)

- 두 개 컴퓨터에서 전송 시 네트워크 손실이 있거나, 전닱이 안 될 때 체크하게 해서 다시 보내게 해서 정확한 데이터를 받을 수 있게 하는 프로토콜이 속함.

- TCP와 UDP 프로토콜을 통해 통신을 활성화

- PORT를 열고, 프로그램들의 전송을 도움

(ex : TCP – 신뢰성, 연결지향적, UDP – 비신뢰성, 비연결성, 실시간)


## (5) 세션(Session)

- 데이터가 통신하기 위한 논리적 연결 담당.

- 통신하는 동안 송신, 수신측이 연결이 유지되도록 도와준다

- TCP/IP 세션의 생성/삭제를 담당

(ex : Socket, API)

## (6) 표현(Presentation)

- 데이터 표현에 대한 독립성을 제공하고 암호화

- 파일 인코딩, 명령어를 포장하고 압축하고 암호화

(ex : JPEG, MPEG)

## (7) 응용(Applictaion)

- 최종 목적지. 응용 프로세스와 직접 관계해서 일반적인 응용 서비스 수행

- 사용자 인터페이스, 전자우편, 데이터베이스 관리 등의 서비스 제공

(ex : HTTP, FTP, DNS)

# 2. **TCP 3 & 4 way handshake**

**(3 way handshake)**

![Untitled 1](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/2c200f3a-539e-4ece-9441-d39ba09194da)


1. 클라이언트가 서버에게 SYN 패킷을 보냄(seq=x)

2. 서버가 SYN(x)를 받고, 클라이언트로 받았다는 신호인 ACK와 SYN패킷을 보냄

(seq=y, ACK=x+1)

3. 클라이언트는 서버의 응답은 ACK(x+1)와 SYN(y) 패킷을 받고, ACK(y+1)를 서버로 보냄

**(4 way handshake)**

![Untitled 2](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/114a88bc-5dbe-4c61-90ab-46fadbf31c2f)


1. 클라이언트는 서버에게 연결을 종료한다는 FIN 플래그를 보냄

2. 서버는 FIN을 받고 확인의 증거인 ACK를 클라이언트를 보냄

**(데이터전송을 위해 TIMEOUT)**

3. 데이터를 모두 보냈다면, 연결이 종료되었다는 FIN을 보냄

4. 클라이언트는 FIN을 받고, 확인의 증거인 ACK를 서버에게 보냄

**(보내는 동안은 WAIT)**

(서버는 ACK를 받은 후 소켓을 닫고, TIME_WAIT가 지나면 클라이언트도 닫음)

(4번의 통신이 완료되면 연결이 해제됨)

# 3. **대칭키 & 공개키**

## (1) 대칭키(Symmetric Key)

- 암호화와 복호화에 같은 암호키를 사용하는 알고리즘 
- 동일한 키를 주고받기 때문에, 매우 빠름 
- 단, 대칭키 전달과정에서 해킹 위험에 노출  

## (2) 공개키(Public Key)

- 암호화와 복호화에 사용하는 암호키를 분리한 알고리즘 
- 자신이 가진 고유한 암호키(개인키)로만 복호화 가능한 암호키(공개키)를 공개  

## (3) 공개키 암호화 방식 진행과정

1) A가 공개된 “B의 공개키”를 이용해 평문을 암호화하여 B에게 전송 
2) B가 본인의 개인키를 사용해 복호화 하여 평문을 확인 
3) B가 “A의 공개키”를 사용해 응답을 암호화하여 A에게 전송 
4) A가 본인의 개인키를 사용해 복호화 하여 응답을 확인

# 4. **HTTP & HTTPS**

## (1) HTTP(Hyper Text Transfer Protocol)

- 인터넷 상에서 클라이언트와 서버가 자원을 주고받을 때의 통신 규약
- 텍스트 교환이므로, 전송과정에서 내용을 가로채서 노출 당할 위험이 있음

## (2) HTTPS(Hyper Text Transfer Protocol Secure)

- 인터넷 상에서 정보를 암호화하는 SSL 프로토콜을 사용해 클라이언트와 서버가 자원을 주고받을 때의 통신 규약
- HTTP는 텍스트를 암호화한다. (공개키 암호화 방식으로)

## (3) HTTPS 통신 흐름

1) 서버A를 만드는 기업은 HTTPS를 적용하기 위해 공개키와 개인키를 만듦

2) 신뢰할 수 있는 CA(Certificate Authority)기업을 선택해, 그 기업에게 공개키 관리위임

3) CA기업은 A서버 공개키, 공개키 암호화방법을 담은 인증서를 만들고 CA기업의 개인키로 암호화해서 A서버에게 제공

4) A서버는 암호화된 인증서를 갖게 됨

5) 클라이언트가 main.html 파일을 달라고 A서버에 요청했다고 가정했을 때,

HTTPS 요청이 아니므로 CA기업이 A서버의 정보를 CA 기업의 개인키로 암호화한 인증서를 받음. (CA 기업의 공개키는 브라우저가 이미 알고 있다.)

6) 브라우저는 해독한 뒤 A서버의 공개키를 얻게 되어 이후 통신이 이루어짐

# 5. **로드밸런싱(Load Balancing)**

### (1) 개요

- 웹사이트에 접속하는 인원의 급격한 증가로 서버 1대가 감당할 수 있는 트래픽 수가 너무나 많음. 대응 방안으로는 하드웨어 성능 향상(Scale-up) 또는 여러 대의 서버를 두는 것(Scale-out)임
- 하지만, 하드웨어 성능자체를 올리는 비용이 더 비쌀 뿐더러, 서버가 여러 대라면 무중단 서비스를 제공할 수 있어 Scale-out이 더 효과적임
- 이 때, 여러 서버에 균등하게 트래픽을 분산시켜주는 것을 **로드밸런싱** 이라 함.
- Client와 Server 사이에 로드밸런서를 하나 두고, 부하가 일어나지 않도록 여러 서버에 분산 시켜 주도록 한다

### (2) 로드 밸런서가 서버를 선택하는 방식

- 라운드 로빈(Round Robin) : CPU 스케쥴링의 라운드 로빈 방식 활용
- 최소 연결(Least Connections) : 연결 개수가 가장 적은 서버 선택
    - (트래픽으로 인해 세션이 길어지는 경우 권장)
- 소스 (Source) : 사용자 IP를 해싱 하여 분배
    - (특정 사용자가 항상 같은 서버로 연결되는 것 보장)

### (3) 로드 밸런서 장애 대비

- 로드 밸런서에 문제가 생길 수 있으므로 로드 밸런서를 이중화하여 대비 (Active와 Passive 상태)

# 6. **TCP/IP(Transmission Control Protocol)**

- **흐름제어 & 혼잡제어**
    - TCP 통신이란? : 네트워크 통신에서 신뢰적인 연결방식.
    - TCP는 기본적으로 unrealiable network에서, reliable network를 보장할 수 있도록 한다.
    - reliable network를 보장한다는 것은 4가지 문제점 존재
        
        1) 손실 : packet이 손실될 수 있는 문제 
        
        2) 순서 바뀜 : packet의 순서가 바뀌는 문제 
        
        3) Congestion : 네트워크가 혼잡한 문제 
        
        4) Overload : reciever가 overload 되는 문제     
        

### * 흐름제어/혼잡제어란?

1) 흐름제어 : 송신측과 수신측의 데이터 속도 차이를 해결하기 위한 기법 

- Flow Control은 receiver가 packet을 지나치게 많이 받지 않도록 조절하는 것

2) 혼잡제어 : 송신측의 데이터 전달과 네트워크의 데이터 처리 속도 차이를 해결하기 위한 기법  

### * 전송의 전체과정

1) Application layer : 송신 application layer가 socket에 data를 씀 

2) Transport layer : data를 segment에 감싼다. 그리고 network layer에 넘김 

3) 하위계층에서 receiving node로 전송됨. 이때, sender의 send buffer에 data를 저장하고, receiver는 receive buffer에 data 저장 

4) application에서 준비가 되면 이 buffer에 있는 것을 읽음 

5) 따라서 flow control의 핵심은 이 receiver buffer가 넘치지 않게 하는 것임 

6) 따라서 receiver는 RWND(Receive Window) : receive buffer의 남은 공간을 홍보  

### * 흐름제어(Flow Control)

- **송신측의 데이터 전송량을 수신측에 따라 조절하는 것**을 흐름제어라 부름
- 송신측의 속도가 빨라, 수신측이 데이터 처리를 정상적으로 할 수 없다면 문제 발생
- 수신측에서 제한된 용량을 초과한 이후 도착하는 데이터는 손실 가능성이 크며, 손실될 경우 불필요한 응답과 데이터 전송이 빈번이 발생

* 해결방법 

1) Stop and wait : 매번 전송한 패킷에 대해 확인 응답을 받아야만 그 다음 패킷을 전송 

2) Sliding Window 

3) 동작방식 

4) Window  

### * 혼잡제어(Congestion Control)

- 송신측의 데이터는 지역망이나 인터넷으로 연결된 대형 네트워크를 통해 전달.
- 만약, 한 라우터에 데이터가 몰릴 경우, 자신에게 온 데이터를 모두 처리할 수 없게 된다.
- 이런 경우, 호스트들은 또다시 재전송을 하게 되고 혼잡만 가중시켜 오버플로우나 데이터 손실을 발생시킨다.
- 따라서, 패킷 수가 과도하게 증가하는 **네트워크의 혼잡을 피하기 위해 송신측에서 보내는 데이터의 전송속도를 강제로 줄이게** 되는데, 이러한 작업을 혼잡제어라 한다.

# 7. **UDP(User Datagram Protocol)**

### UDP 통신이란?

- 데이터를 데이터그램 단위로 처리하는 프로토콜
- 비연결형, 신뢰성 없는 전송 프로토콜이다.
- 데이터그램 단위로 쪼개면서 전송을 해야하기 때문에 전송 계층이다.

***# 데이터그램 방식 : 패킷 교환에서 각 패킷이 독립적으로 처리되어 목적지까지 도달하는 방식***  

- TCP와 UDP가 나온 이유

1) IP의 역할은 Host to Host 만을 지원. 장치에서 장치로 이동은 IP로 해결되나, 하나의 장비안에서 수많은 프로그램이 통신할 경우에는 IP만으로 한계가 있음 

2) 또한, IP에서 오류가 발생한다면 ICMP에서 알려줌. 하지만, ICMP는 알려주기만 할 뿐 대처하지는 못하여 IP보다 위에서 처리를 해주어야 함 

3) 1번 해결을 위해 포트번호가 나오고, 2번 해결을 위해 TCP와 UDP가 나옴 

***#ICMP: 인터넷 제어 메시지 프로토콜. 네트워크 컴퓨터 내의 오류 메세지를 발생시킴.***  

*** TCP와 UDP가 오류를 해결하는 방법** 

- TCP : 데이터 분실, 중복, 순서의 뒤바뀜 등을 자동으로 보정
- UDP : TCP와는 다르게 에러가 발생할 수 있음.

*** UDP는 왜 사용할까?** 

- UDP의 주 장점은 데이터의 신속성이다.
- 따라서, 주로 실시간 방송이나 온라인게임에서 주로 사용된다.
- DNS(Domain Name Service)에서 UDP를 쓰는 이유
    
    1) Request 양이 작음 -> UDP Request에 담길 수 있음 
    
    2) 3 way handshaking이 필요 없음 
    
    3) UDP는 비신뢰적이나, 신뢰성은 application layer에 추가 가능하다. 
    
    4) DNS : 포트 53번 
    
    5) 하지만 크기가 512Byte를 넘으면, TCP를 사용해야 한다
    

# 8. **HTTP 요청 흐름(웹브라우저에서의 요청)**

![Untitled 3](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/519737b0-80ad-4d7b-a5db-3f30947d6ded)


1) 2) 사용자가 웹 브라우저를 통해 찾고 싶은 웹페이지의 URL 주소 입력

3) 사용자가 입력한 URL주소 중에서 도메인 부분을 DNS서버에서 검색

4) DNS 서버에서 해당 도메인에 해당되는 IP주소를 찾아 사용자가 입력한 URL 정보와 함게 전달.

5) 6) 웹 페이지 URL 정보와 전달 받은 IP주소는 HTTP프로토콜을 사용해 HTTP 요청 메시지를 생성. 이렇게 생성된 HTTP 요청 메시지는 TCP 프로토콜을 사용하여 인터넷을 거쳐 해당 IP주소의 컴퓨터로 전송

7) 이렇게 도착한 HTTP 요청 메시지는 HTTP프로토콜을 사용하여 인터넷을 거쳐 해당 IP주소의 컴퓨터로 전송

8) 웹 서버는 도착한 웹 페이지 URL 정보에 해당하는 데이터를 검색

9) 10) 검색된 웹 페이지 데이터는 또 다시 HTTP 프로토콜을 사용하여 HTTP 응답 메시지생성. 이렇게 생성된 HTTP 응답 메시지는 TCP 프로토콜을 사용해 인터넷을 거쳐 원래 컴퓨터로 전송

11) 도착한 HTTP 응답 메시지는 HTTP 프로토콜을 사용하여 웹 페이지 데이터로 변환

12) 변환된 웹 페이지 데이터는 웹 브라우저에 의해 출력되어 사용자가 볼 수 있음.

출처 : [https://gyoogle.dev/blog/computer-science/](https://gyoogle.dev/blog/computer-science/network/OSI%207%EA%B3%84%EC%B8%B5.html)