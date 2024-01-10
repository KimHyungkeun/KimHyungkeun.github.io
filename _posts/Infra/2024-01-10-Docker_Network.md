---
title: Docker Network
date: YYYY-MM-DD HH:MM:SS +00:00
categories: [Infra, Docker]
tags:
  [
    Infra,
    Docker
  ]
---

# Docker Network(2022/05/06)

![image](https://user-images.githubusercontent.com/12759500/229356175-c69ba5f9-1708-4144-8be0-d7971b96822a.png)

- 도커 컨테이너를 생성하면 172.17.0.0 대역의 IP를 부여받음
- 컨테이너 내부에서 할당되는 eth0 이름의 인터페이스가 있고, 
호스트 네트워크 브리지 docker0에 바인딩 되는 veth(virtual ethernet)이 존재한다.
- 컨테이너의 eth0과 호스트 veth가 서로 연결되어 통신된다

# Network 종류

1) Bridge : 하나의 Host 내에서 여러 Container들이 소통하도록 설정

2) Host : Host와 동일한 설정의 네트워크를 Container에서도 그대로 쓸 수 있음

![image](https://user-images.githubusercontent.com/12759500/229356181-38dd647b-7af2-4dc7-a273-362b07013d20.png)

3) None : 모든 네트워크를 끊어서 단절된 상태가 된다

4) Container : 한 컨테이너 네트워크 설정을, 다른 컨테이너에서 그대로 이용한다.
(상황 : volumes_from_container라는 네트워크 환경을 
network_container 라는 이름의 새로운 컨테이너에서 그대로 사용하도록 한다.)

![image](https://user-images.githubusercontent.com/12759500/229356192-5ff7bbab-01a9-4b4a-8b98-6810f2ae2be2.png)

5) Overlay : 여러 Host에 분산되어 돌아가는 Container들 간의 네트워킹을 위해 설정

# 1. Create Network

```bash
$ docker network create --driver bridge alpine-net
$ docker network ls
```

![image](https://user-images.githubusercontent.com/12759500/229356207-6c9a619f-daa8-402f-9bff-f1e7c231dd27.png)

# 2. Inspect Network

```bash
$ docker network inspect alpine-net
```

![image](https://user-images.githubusercontent.com/12759500/229356214-7714008e-968e-4901-abed-35724d09ebd3.png)

# 3. Connect Container to an Existing Network

```bash
$ docker run -dit --name alpine1 --network alpine-net alpine ash # ash : alpine 기본쉘

$ docker run -dit --name alpine2 --network alpine-net alpine ash

$ docker run -dit --name alpine3 alpine ash

$ docker run -dit --name alpine4 --network alpine-net alpine ash

$ docker network connect bridge alpine4

# (--network 옵션을 통해 alpine-net이라는 네트워크에 접근한다)
```

![image](https://user-images.githubusercontent.com/12759500/229356223-da86130f-15f1-4d94-8f60-50a366965008.png)

```bash
$ docker network inspect bridge : bridge 네트워크의 상태 정보 확인
(alpine3, alpine4가 접속됨)
```

![image](https://user-images.githubusercontent.com/12759500/229356232-719bd16c-ce6f-44c6-b372-04e6296c07ae.png)

```bash
$ docker network inspect alpine-net : alpine-net의 상태정보 확인
(alpine1, alpine2, alpine4 접속 확인)
```

![image](https://user-images.githubusercontent.com/12759500/229356240-6583847d-6b8e-4366-8e52-e9241fd49921.png)

- alpine1에 접속하여 alpine2,3,4의 접속을 확인

```bash
$ docker container attach alpine1
/# ping -c 2 alpine2 : alpine2의 ping확인 (정상)
/# ping -c 2 alpine4 : alpine4의 ping확인 (정상)
/# ping -c 2 alpine3 : alpine3의 ping확인 (응답없음) -> alpine3은 alpine-net 접속이 아님
```

![image](https://user-images.githubusercontent.com/12759500/229356256-bb94ec2f-c82f-4717-bdc9-9106ba7f9920.png)

- alpine4에 접속하면 alpine1,2,3,4가 모두 보인다. (alpine-net, bridge 네트워크 둘다 있으므로)

![image](https://user-images.githubusercontent.com/12759500/229356265-300d00c7-b022-4849-b0df-7280efe05488.png)

# 4. Disconnect Container from the Existing Network

```bash
$ docker network disconnect (네트워크 이름) (컨테이너ID/컨테이너 이름)
( alpine-net 네트워크에서 alpine1 컨테이너와의 연결을 끊는다.)
```

ex) alpine-net 네트워크에는 alpine1,2,4가 연결되어있다.
(Before)

![image](https://user-images.githubusercontent.com/12759500/229356271-a505cf1d-5890-4daa-a23a-322e14179534.png)

(After) : alpine1 제거

![image](https://user-images.githubusercontent.com/12759500/229356281-2681a012-7afe-4bb1-9bcd-55f6a6de9138.png)





