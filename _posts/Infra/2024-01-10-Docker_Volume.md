---
title: Docker Volume
date: YYYY-MM-DD HH:MM:SS +00:00
categories: [Infra, Docker]
tags:
  [
    Infra,
    Docker
  ]
---


# Docker Volume(2022/05/06)

- Docker Container 데이터 보존 방법에는 Volume과 Bind Mount가 존재한다

# 1. Docker Volume

- 컨테이너가 삭제되어도, 그 내부의 데이터를 보존시키고 싶을 때 Volume을 이용하여 데이터를 보관하도록 한다

![image](https://user-images.githubusercontent.com/12759500/229355979-248d03f9-c11e-42a5-a3d1-23b33dc2bb82.png)

![image](https://user-images.githubusercontent.com/12759500/229355985-491cd9ff-865b-4e0e-8da0-4711cab515ba.png)

ex) 도커 볼륨 생성

```bash
$ docker volume create --name myvolume # myvolume이라는 이름을 가진 볼륨 생성
$ docker run -it --name myvolume_1 \
-v myvolume:/root/ \
alpine
```

```bash
/# echo hello, volume! >> /root/volume
```

```bash
$ docker run -it —name myvolume_2 \
-v myvolume:/root/ \
alpine
```

```bash
/# cat /root/volume
```

![image](https://user-images.githubusercontent.com/12759500/229356004-4059c350-f3a6-4702-84c0-a44ed48a83d8.png)

- -v 대신 —mount 옵션을 쓸 수도 있다.

```bash
ex)
$ docker run -it —name myvolume_1 \
--mount type=volume, source=myvolume, target=/root/ \
alpine
```

- myvolume이라는 볼륨이 실제 어디에 저장되는지 확인

```bash
$ docker inspect —type volume myvolume
```

![image](https://user-images.githubusercontent.com/12759500/229356015-9dabc70e-2306-4e63-af2b-6d46b9f617a5.png)

→ Windows Docker Desktop에서는 /var/lib/docker/~~~는 어떻게 접근해야 하는지 모름

→ 설치환경마다 차이는 있겠으나, 기본적으로 Docker Data들은 모두
C:\Users\user\AppData\Local\Docker\wsl\data에 있는 etx4.vhdx라는 이미지 파일에 저장되어있다.

![image](https://user-images.githubusercontent.com/12759500/229356033-7ad46094-7786-4658-a058-3d5ab3204e92.png)

PowerShell을 열어서 etx4.xhdx를 베이스로 한 컨테이너를 열어서 접근한다.

```bash
$ docker run -v /:/data -it alpine
# chroot /data
# cd /var/lib/docker
```

```bash
$ ls /var/lib/docker/containers : 현재 존재하는 컨테이너 확인
```

![image](https://user-images.githubusercontent.com/12759500/229356039-a13e62f7-d1c3-4527-a9ed-574bfcda4559.png)

```bash
$ ls /var/lib/docker/volumes : 현재 존재하는 볼륨 확인
```

![image](https://user-images.githubusercontent.com/12759500/229356045-463297ee-70c0-4353-9f81-39a53c550cff.png)

참고 : [https://velog.io/@ette9844/Windows10-에서-varlibdocker-찾기#:~:text=설치 환경마다 경로에,vhdx 형식으로 저장된다](https://velog.io/@ette9844/Windows10-%EC%97%90%EC%84%9C-varlibdocker-%EC%B0%BE%EA%B8%B0#:~:text=%EC%84%A4%EC%B9%98%20%ED%99%98%EA%B2%BD%EB%A7%88%EB%8B%A4%20%EA%B2%BD%EB%A1%9C%EC%97%90,vhdx%20%ED%98%95%EC%8B%9D%EC%9C%BC%EB%A1%9C%20%EC%A0%80%EC%9E%A5%EB%90%9C%EB%8B%A4).

참고 : [https://stackoverflow.com/questions/60408574/how-to-access-var-lib-docker-in-windows-10-docker-desktop](https://stackoverflow.com/questions/60408574/how-to-access-var-lib-docker-in-windows-10-docker-desktop)

# 2. Bind Mount

- 호스트 OS쪽에 원하는 경로를 지정하여, 그 경로에 원하는 데이터들을 보존한다

![image](https://user-images.githubusercontent.com/12759500/229356060-ec078cac-be1d-4ae9-b03d-ac0e871a2af2.png)

![image](https://user-images.githubusercontent.com/12759500/229356067-8dfa5874-c16b-43c1-bfb8-727b42be2805.png)

```bash
ex)
$ docker run -d \
--name wordpressdb_hostvolume \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
-v C:\Users\hyung\Documents\wordpress_db:/var/lib/mysql \
mysql:5.7

 
```

```bash
ex2)
$ docker run -d \
--name wordpressdb_hostvolume \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
—mount type=bind, source=C:\Users\hyung\Documents\wordpress_db, target=/var/lib/mysql \
mysql:5.7
```

![image](https://user-images.githubusercontent.com/12759500/229356087-b49d9654-000f-435b-a234-fc1be0e937b3.png)

유의사항 : 

1) Windows 진행 시 PowerShell을 이용할 것

2) PowerShell에서의 줄바꿈 문자는 ` 이다.

참고
[https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/)
[https://docs.docker.com/storage/bind-mounts/](https://docs.docker.com/storage/bind-mounts/)

# 3. Volume Container

- 볼륨을 사용하는 컨테이너를 다른 컨테이너와 공유하는 것을 말한다
- container 생성 시, —volumes-from 옵션을 설정하면 -v 또는 —volume 옵션을 적용한 컨테이너의 볼륨 디렉터리 공유가 가능하다.

![image](https://user-images.githubusercontent.com/12759500/229356095-3908f360-d470-44c7-a172-29eaf7793654.png)

ex) txt 파일 생성을 이용한 볼륨 컨테이너 

1) Windows 상의 가상 리눅스 환경으로 접근한다. (WSL2가 설치되어 있음)

```bash

$ docker run -v /:/data -it alpine
$ root@@842db9ad4579:~/                                                                                                                                                                      
```

2) 볼륨으로 사용될 컨테이너와, 추가 컨테이너 2개를 생성
—volumes-from을 이용해 컨테이너를 볼륨처럼 사용한다.

```bash
$ docker run -itd --name vol_container -v /root/volumes:/root alpine
$ docker run -itd --name vol_test1 —volumes-from vol_container alpine
$ docker run -itd --name vol_test2 —volumes-from vol_container alpine
```

3) 이미지에 원래 존재하던 디렉터리에 
호스트 볼륨을 공유하면 컨테이너의 디렉터리 자체가 덮어 씌워짐

```bash
$ docker exec -it vol_test1 touch /root/happy.txt
$ ls
$ docker exec -it vol_test2 ls /root
$ docker exec -it vol_container ls /root
```

![image](https://user-images.githubusercontent.com/12759500/229356104-79ff145c-6a78-4953-bcb3-44f979242314.png)

4) world.txt를 추가 생성

```bash
$ docker exec -it vol_container touch /root/world.txt
$ docker exec -it vol_test2 ls /root
$ docker exec -it vol_test1 ls /root
$ ls
```

![image](https://user-images.githubusercontent.com/12759500/229356108-6ad8e5d4-6fc2-4023-8663-8acab83bef75.png)



