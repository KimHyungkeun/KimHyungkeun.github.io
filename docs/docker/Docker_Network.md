---
layout: default
title: Docker Network
parent: Docker
nav_order: 5
---

# Docker Network(5/6)

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled.png)

- 도커 컨테이너를 생성하면 172.17.0.0 대역의 IP를 부여받음
- 컨테이너 내부에서 할당되는 eth0 이름의 인터페이스가 있고, 
호스트 네트워크 브리지 docker0에 바인딩 되는 veth(virtual ethernet)이 존재한다.
- 컨테이너의 eth0과 호스트 veth가 서로 연결되어 통신된다

# Network 종류

1) Bridge : 하나의 Host 내에서 여러 Container들이 소통하도록 설정

2) Host : Host와 동일한 설정의 네트워크를 Container에서도 그대로 쓸 수 있음

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%201.png)

3) None : 모든 네트워크를 끊어서 단절된 상태가 된다

4) Container : 한 컨테이너 네트워크 설정을, 다른 컨테이너에서 그대로 이용한다.
(상황 : volumes_from_container라는 네트워크 환경을 
network_container 라는 이름의 새로운 컨테이너에서 그대로 사용하도록 한다.)

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%202.png)

5) Overlay : 여러 Host에 분산되어 돌아가는 Container들 간의 네트워킹을 위해 설정

# 1. Create Network

```bash
$ docker network create --driver bridge alpine-net
$ docker network ls
```

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%203.png)

# 2. Inspect Network

```bash
$ docker network inspect alpine-net
```

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%204.png)

# 3. Connect Container to an Existing Network

```bash
$ docker run -dit --name alpine1 --network alpine-net alpine ash # ash : alpine 기본쉘

$ docker run -dit --name alpine2 --network alpine-net alpine ash

$ docker run -dit --name alpine3 alpine ash

$ docker run -dit --name alpine4 --network alpine-net alpine ash

$ docker network connect bridge alpine4

# (--network 옵션을 통해 alpine-net이라는 네트워크에 접근한다)
```

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%205.png)

```bash
$ docker network inspect bridge : bridge 네트워크의 상태 정보 확인
(alpine3, alpine4가 접속됨)
```

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%206.png)

```bash
$ docker network inspect alpine-net : alpine-net의 상태정보 확인
(alpine1, alpine2, alpine4 접속 확인)
```

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%207.png)

- alpine1에 접속하여 alpine2,3,4의 접속을 확인

```bash
$ docker container attach alpine1
/# ping -c 2 alpine2 : alpine2의 ping확인 (정상)
/# ping -c 2 alpine4 : alpine4의 ping확인 (정상)
/# ping -c 2 alpine3 : alpine3의 ping확인 (응답없음) -> alpine3은 alpine-net 접속이 아님
```

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%208.png)

- alpine4에 접속하면 alpine1,2,3,4가 모두 보인다. (alpine-net, bridge 네트워크 둘다 있으므로)

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%209.png)

# 4. Disconnect Container from the Existing Network

```bash
$ docker network disconnect (네트워크 이름) (컨테이너ID/컨테이너 이름)
( alpine-net 네트워크에서 alpine1 컨테이너와의 연결을 끊는다.)
```

ex) alpine-net 네트워크에는 alpine1,2,4가 연결되어있다.
(Before)

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%2010.png)

(After) : alpine1 제거

![Untitled](Docker%20Network(5%206)%2060b84bb659c343769ff9e3597620e663/Untitled%2011.png)

[Docker Network Scenario](https://www.notion.so/Docker-Network-Scenario-f9b126214eb94566962a3a181bc109d4)

