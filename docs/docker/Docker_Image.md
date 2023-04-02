---
layout: default
title: Docker Image
parent: Docker
nav_order: 2
---

# Docker Image(5/3)

# 1. **Write a Dockerfile**

* 도커 이미지를 이용해서

```bash
$ docker create <이미지이름>
```

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled.png)

- Dockerfile을 만드는법
Docker File 이란 Docker Image를 만들기 위한 설정 파일.
컨테이너가 어떻게 행동해야 할지 설정들을 정의해준다
- 도커 파일 만드는 순서(도커 이미지가 필요한것이 무엇인지 생각)
1) 베이스 이미지를 명시(파일 스냅샷에 해당)
2) 추가적으로 필요한 파일을 다운 받기 위한 몇가지 명령어 명시. (파일 스냅샷 해당)
3) 컨테이너 시작 시 실행 될 명령어를 명시 (시작 시 실행 될 명령어에 해당)

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%201.png)

- **베이스 이미지란?**
- 도커 이미지는 여러개의 레이어로 되어 있다.
그 중에서 베이스 이미지는 이 이미지의 기반이 되는 부분이다.
- 레이어는 중간 단계의 이미지이다.
(베이스 이미지는 쉽게 Winodws, MacOS, Linux와 같은 OS를 뜻한다)

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%202.png)

# 2. **Build Image based on Dockerfile**

1) VSCode를 열어서 Dockerfile이라는 이름의 문서를 만듦

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%203.png)

```bash
    2) $ docker build ./ or $ docker build . 실행
```

(MacOS 예제에서는 아래와 같이 나오지만 Windows에선 에러 발생)

(Windows 실패)
(5/4 문제해결 : “Dockerfile” 이어야 하는데 f를 F로 적어서 없는 파일로 인식된 것이었음)

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%204.png)

(Windows 성공)

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%205.png)

(MacOS 성공)

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%206.png)

# 3. **Build Image based on an Any Existing Image in Dockerfile**

$ git clone https://github.com/docker/doodle.git
(내려받은 저장소에서 cheers2019 폴더로 지정)
$ docker build -t {github_id}/cheers2019 .
(-t : 이미지 이름 지정)
$ docker run -it —rm {github_id}/cheers2019 .
(-i : 표준입력 활성화, -t : TTY모드(pseudo-TTY) , —rm : 프로세스 종료 시 컨테이너 자동 삭제)

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%207.png)

# 4. **Tag Image**

ex1)
$ docker image tag {이미지이름}:{버전} {이미지이름}:{새로적을 버전}
$ docker image tag alpine:latest alpine:custom_3.10

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%208.png)

ex2)
$ docker image tag alpine:latest vincent:latest

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%209.png)

# 5. **Remove Image**

$ docker rmi {이미지 이름}

![Untitled](Docker%20Image(5%203)%20fe5f269fa81c4264bf88e8a1834ef333/Untitled%2010.png)