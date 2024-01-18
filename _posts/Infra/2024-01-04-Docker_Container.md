---
title: Docker Container
date: 2024-01-04 00:00:00 +00:00
categories: [Infra, Docker]
tags:
  [
    Infra,
    Docker
  ]
---

# Docker Container(2022/05/04)

# 1. Docker Run

- run을 하게되면 새로운 container를 만들고 image를 실행시킨다
- $ docker run [옵션] {이미지 이름}
- (옵션) -d : detach모드. 백그라운드로 실행됨
- (옵션) - -name : 이름짓기
- (옵션) - -rm : 프로세스 종료 후 컨테이너 삭제
- $ docker run -it - -rm {이미지 이름} : 해당 컨테이너는 프로세스가 종료되고나면 바로 삭제한다

# 2. Docker Create

- container만을 생성하게 된다

```bash
$ docker create {이미지 이름}
```

```bash
$ docker create -i -t —name [짓고 싶은 이름] {이미지이름}
(-i : STDIN 활성화, -t : pseudo-TTY 활성화, —name : 이름짓기)
⇒ -i, -t가 있어야 attach모드 시 입출력이 가능하다

**TTY (Teletypewriter) : 리눅스에서 콘솔 창에 표현되는 프롬프트
(-i 옵션이 없으면, 프롬프트는 띄워지지만 입출력이 불가능하다)
(-t 옵션이 없으면, 입출력은 가능하나 프롬프트가 띄워지지 않아서 가독성 어려움)**
```

# 3. Docker List Container

- 현재 실행중인 컨테이너의 상태를 보여준다

```bash
$ docker ps (or) $ docker container ls
```

```bash
$ docker ps —format 'table{{.Names}}\table{{.Image}}’ (테이블 형태처럼 상태확인이 가능하다)
```

![image](https://user-images.githubusercontent.com/12759500/229355898-5e898fd2-bcb3-45bb-b290-757c911138b2.png)

```bash
(옵션) -a : 실행 중인 것, 중지된 컨테이너 모두를 다 보여준다
```

![image](https://user-images.githubusercontent.com/12759500/229355904-738c71ff-c123-48f9-8d3a-ebc67701602f.png)

# 4. Docker Stop

- 실행중인 container를 중지시킨다
($ docker kill : stop은 gracefully 하게 작업이 끝날 때 까지를 기다려주지만, 
kill은 현재 상태 여부 상관없이 강제 종료 시킨다)

![image](https://user-images.githubusercontent.com/12759500/229355914-c0782453-ba19-4a16-8582-577e48b1ecdb.png)

- $ docker stop {컨테이너 ID}/{컨테이너이름}
- $ docker kill {컨테이너ID}/{컨테이너이름}

# 5. Docker Start

- container를 실행한다. (Run = create + start)

```bash
$ docker start {컨테이너 ID}/{컨테이너이름}

```

![image](https://user-images.githubusercontent.com/12759500/229355923-21508ad2-5d08-4172-a751-b0c6c040b08c.png)

```bash
$ docker attach {컨테이너 ID}/{컨테이너이름} : 동작하는 해당 컨테이너에 접속한다
(docker create 시 -i, -t 옵션이 붙어야 가능하다)
```

![image](https://user-images.githubusercontent.com/12759500/229355930-dba47195-5334-4e96-8488-a6abdf2b1674.png)

# 6. Docker Restart

- container를 재 실행 시킨다.

```bash
$ docker restart {컨테이너 ID}/{컨테이너이름}
```

# 7. Docker logs

- container가 실행되는 동안 기록되었던 로그 들을 확인한다

```bash
$ docker logs {컨테이너ID}/{컨테이너이름}
```

![image](https://user-images.githubusercontent.com/12759500/229355942-ca609f1e-0854-45db-8bef-7a1fed168165.png)

# 8. Docker rm

- 중지중인 container를 삭제한다. (실행중인 컨테이너는 삭제 안됨)

```bash
$ docker rm {컨테이너ID}/{컨테이너이름}
```

```bash
$ docker container prune : 정지중인 컨테이너 **“모두”** 지우고 싶을때
```

```bash
$ docker rm `docker ps -a -q`
```

```bash
$ docker system prune : 이미지, 정지 중인 컨테이너, 네트워크 **모두**
 다 지워버리고 싶다면
```

# 9. Docker exec

- 현재 실행중인 컨테이너에 대해 추가 명령을 내리는 커맨드

```bash
$ docker exec {컨테이너ID}/{컨테이너이름} {cmd}
```

참고 : [https://darrengwon.tistory.com/791](https://darrengwon.tistory.com/791)

