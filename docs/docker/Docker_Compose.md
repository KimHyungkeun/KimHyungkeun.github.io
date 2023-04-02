---
layout: default
title: Docker Compose
parent: Docker
nav_order: 6
---

# Docker Compose(5/9)

# 1. Docker Compose란?

**정의 : 다중 컨테이너 도커 어플리케이션을 정의하고 실행하기 위한 도구**
→ 작업을 진행하다 보면 여러 컨테이너를 생성해야 할 경우가 온다.
→ 이 때, 컨테이너 생성을 위해 일일이 명령어를 입력하는 것은 시간이 오래 걸린다.
→ 따라서, compose를 통해 여러 개의 컨테이너를 하나의 프로젝트로서 다룰 수 있게 한다

(Windows는 Docker Desktop을 설치하면, 자동적으로 docker-compose가 설치되어 있다)

```bash
$ docker-compose -v
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled.png)

(docker compose를 사용하려면, docker-compose.yml이라는 파일이 필요하다)

(주의 : 들여쓰기 할때 Tab은 인식을 못함. 스페이스 2번으로 들여쓰기)

```
version: "3.9"  # optional since v1.27.0
services:
  web:
    build: .
    ports:
      - "8000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

참고 : [https://docs.docker.com/compose/](https://docs.docker.com/compose/)

# 2. Single Service

ex) postgresql DB를 compose를 통해 올리기
1) docker-compose.yml 작성

```bash
version: "3.9"  # optional since v1.27.0
services:
  postgres-db:
    container_name : postgres_compose
    image : postgres:latest
    volumes :
      - compose_volume:/var/lib/postgresql/data
    ports : 
      - "5432:5432"
    environment : 
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"

volumes :
  compose_volume :
    external : true
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%201.png)

2) docker compose 실행

```bash
$ docker-compose up -d # -d : 백그라운드 실행
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%202.png)

3) docker compose 실행 확인

```bash
$ docker ps
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%203.png)

4) docker compose를 중단

```bash
$ docker-compose down
```

# 3. Multiple Service

ex) postgresql db, mysql db 모두 올려보기

1) docker-compose.yml 작성

```bash
version: "3.9"  # optional since v1.27.0
  services:
    postgres-db:
      container_name : postgres_compose
      image : postgres:latest
      volumes :
        - compose_volume:/var/lib/postgresql/data
      ports :
        - "5432:5432"
      environment :
        POSTGRES_USER: "postgres"
        POSTGRES_PASSWORD: "postgres"
    mysql-db :
      container_name : mysql_compose
        image : mysql:5.7
          volumes :
            - compose_volume_mysql:/var/lib/mysql
    environment :
      MYSQL_DATABASE: "wordpress"
      MYSQL_ROOT_PASSWORD: "password"
  volumes :
    compose_volume :
      external : true
    compose_volume_mysql:
      external : true
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%204.png)

2) docker-compose 실행

```bash
$ docker-compose up -d
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%205.png)

3) mysql 접속

```bash
$ docker exec -it mysql_compose bash
root@{containerID}:/# mysql -u root -p
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%206.png)

4) postgresql 접속

```bash
$ docker exec -it postgres_compose
root@{containerID}:/# psql -U postgres
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%207.png)

# 4. Multiple Service in a pre-defined network

1) compose_network라는 이름의 네트워크 형성 (네트워크 종류는 bridge)

```bash
$ docker network create —driver bridge compose_network
```

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%208.png)

2) docker-compose.yml 생성

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%209.png)

3) docker-compose up

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%2010.png)

4) [localhost:5050](http://localhost:5050) 접속 (pgadmin 접속)

![Untitled](Docker%20Compose(5%209)%20ce3e953824ee4e5b8d5826a7b4b657a9/Untitled%2011.png)