---
title: Docker Image
date: 2024-01-10 00:00:00 +00:00
categories: [Infra, Docker]
tags:
  [
    Infra,
    Docker
  ]
---

# Docker Image(2022/05/03)

# 1. **Write a Dockerfile**

* 도커 이미지를 이용해서

```bash
$ docker create <이미지이름>
```

![image](https://user-images.githubusercontent.com/12759500/229355798-3ee8ca6b-2294-4092-a7d4-8fa4a3c2fc77.png)

- Dockerfile을 만드는법
Docker File 이란 Docker Image를 만들기 위한 설정 파일.
컨테이너가 어떻게 행동해야 할지 설정들을 정의해준다
- 도커 파일 만드는 순서(도커 이미지가 필요한것이 무엇인지 생각)
1) 베이스 이미지를 명시(파일 스냅샷에 해당)
2) 추가적으로 필요한 파일을 다운 받기 위한 몇가지 명령어 명시. (파일 스냅샷 해당)
3) 컨테이너 시작 시 실행 될 명령어를 명시 (시작 시 실행 될 명령어에 해당)

![image](https://user-images.githubusercontent.com/12759500/229355807-2085cceb-6dff-4cd5-bdd7-c360559ea42b.png)

- **베이스 이미지란?**
- 도커 이미지는 여러개의 레이어로 되어 있다.
그 중에서 베이스 이미지는 이 이미지의 기반이 되는 부분이다.
- 레이어는 중간 단계의 이미지이다.
(베이스 이미지는 쉽게 Winodws, MacOS, Linux와 같은 OS를 뜻한다)

![image](https://user-images.githubusercontent.com/12759500/229355814-dde144d5-da81-4e3d-a2c5-797de8160503.png)

# 2. **Build Image based on Dockerfile**

1) VSCode를 열어서 Dockerfile이라는 이름의 문서를 만듦

![image](https://user-images.githubusercontent.com/12759500/229355818-63bf4b50-fcf5-4a24-b175-4edbc6e8f3fc.png)

```bash
    2) $ docker build ./ or $ docker build . 실행
```

(MacOS 예제에서는 아래와 같이 나오지만 Windows에선 에러 발생)

(Windows 실패)
(5/4 문제해결 : “Dockerfile” 이어야 하는데 f를 F로 적어서 없는 파일로 인식된 것이었음)

![image](https://user-images.githubusercontent.com/12759500/229355824-63377d7b-843c-486c-8017-df53430b91d2.png)

(Windows 성공)

![image](https://user-images.githubusercontent.com/12759500/229355828-2b9d9f4c-6b22-4714-801c-a3035aff564f.png)

(MacOS 성공)

![image](https://user-images.githubusercontent.com/12759500/229355831-817085a4-6288-4bf2-8aa1-0ec1334b73ea.png)

# 3. **Build Image based on an Any Existing Image in Dockerfile**

$ git clone https://github.com/docker/doodle.git
(내려받은 저장소에서 cheers2019 폴더로 지정)
$ docker build -t {github_id}/cheers2019 .
(-t : 이미지 이름 지정)
$ docker run -it —rm {github_id}/cheers2019 .
(-i : 표준입력 활성화, -t : TTY모드(pseudo-TTY) , —rm : 프로세스 종료 시 컨테이너 자동 삭제)

![image](https://user-images.githubusercontent.com/12759500/229355840-56bbda28-90b6-4e74-8071-5bc2dbbb7298.png)

# 4. **Tag Image**

ex1)
$ docker image tag {이미지이름}:{버전} {이미지이름}:{새로적을 버전}
$ docker image tag alpine:latest alpine:custom_3.10

![image](https://user-images.githubusercontent.com/12759500/229355849-29934364-2fb6-4570-b568-98b84810365f.png)

ex2)
$ docker image tag alpine:latest vincent:latest

![image](https://user-images.githubusercontent.com/12759500/229355857-34efbb1d-bedd-4d4a-959c-736e4987e5bc.png)

# 5. **Remove Image**

$ docker rmi {이미지 이름}

![image](https://user-images.githubusercontent.com/12759500/229355871-8eadd637-a0c2-41d2-be21-12e72cbd3223.png)
