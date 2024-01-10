---
title: Docker Introduction
date: YYYY-MM-DD HH:MM:SS +00:00
categories: [Infra, Docker]
tags:
  [
    Infra,
    Docker
  ]
---

# Introduction Docker

# **Introduction to Docker (5/3)**

## 왜 docker를 쓰는가?

Installer 다운 → Installer 실행 → 프로그램 설치 완료
하지만, Installer 실행 중 에러가 발생할 수 있다.
(갖고 있는 서버, 패키지 버전, 운영체제 등등에 따라 프로그램 설치 과정 중에 많은 에러 발생)

**Docker를 쓰게 된다면, Container 단위로 프로그램의 배포 및 관리가 간단하게  가능하다.**

1) Docker란?
- Container를 사용하여 응용프로그램을 더 쉽게 만들고 배포하고 실행할 수 있도록 설계된 도구
- Container 기반의 오픈소스 가상화 플랫폼

2) Docker Image 란?

 - Container는 코드와 모든 종속성을 패키지화 하여 응용 프로그램이 
한 컴퓨팅 환경에서 다른 컴퓨팅 환경으로 빠르고 안정적으로 실행이 되도록 하는 단위이다.

- Container Image는 코드, 런타임, 도구, 시스템 설정과 같은 응용 프로그램을 실행하는데 필요한 모든 것을 포함하는 독립적 실행 소프트웨어 패키지이다

- 이때, Container Image는 런타임에 container가 되고, docker container의 경우라면 docker engine에서 실행될 때, image가 container가 된다.
- 리눅스와 윈도우 기반 애플리케이션 모두에서 사용할 수 있는 컨테이너화된 소프트웨어는 인프라에 관계없이 동일하게 작동한다. 
(소프트웨어를 환경으로부터 격리시키고 개발과 스테이징의 차이에도 불구하고 균일 작동하도록 보장)

![image](https://user-images.githubusercontent.com/12759500/229355722-c01d6be4-2e6e-46b9-9215-39dfc650caaa.png)

3) Docker Container 란?
- 다양한 프로그램, 실행환경 등을 추상화 하여 동일한 인터페이스 제공하여 프로그램 배포 및 관리를 간단히 해주는 것이 Container 이다.
- 기본적으로 컨테이너 내애는 다양한 물건이나 물품을 넣을 수 있다.
- 그리고, 컨테이너에 내용을 담아 운송 수단으로 쉽게 옮길 수 있다.
- 이처럼, 서버 내에서도 Container란 것을 이용하여 프로그램을 손쉽게 이동 배포 관리 해준다

4) Docker 사용시 흐름
1. 도커 사용시에는 도커 CLI에 커맨드 입력
2. 그러면 도커 서버(도커 Daemon)이 커맨드를 받아서 실행

![image](https://user-images.githubusercontent.com/12759500/229355736-7bd3fa58-8de8-4d0f-b5a4-51c63746fa5c.png)

![image](https://user-images.githubusercontent.com/12759500/229355743-94d5f1de-38d5-4c20-90dc-862fa2ac2ecf.png)

설명
($ docker run hello)
docker : docker 클라이언트임을 명시
run : container 생성
hello-world : hello-world라는 이름을 가진 이미지

1) 도커 클라이언트에 커맨드 입력하여 서버로 요청 보냄
2) 서버에서 hello-world라는 이미지가 로컬에 cache 되어있는지 확인
3) 현재는 없기에 Unable to find image 문구 표시
4) Docker hub이라는 이미지가 저장되어 있는 곳에 가서 그 이미지를 가져오고 로컬에 Cache 보관
5) 그 후 이제는 이미지가 있으니 그 이미지를 이용하여 컨테이너 생성

![image](https://user-images.githubusercontent.com/12759500/229355764-2371d1f9-886e-4f25-948f-1e2b3937659e.png)

*** Docker 사용과 VM 사용시의 차이** 
- 기존의 가상화 환경에서는 Hypervisor라는 통합관리 체계를 이용해, 각각의 Guest OS를 설정하였다. 
- 그렇기 때문에, 그 무게가 무거웠지만
- 반면, Docker Engine을 사용하게 되면 컨테이너 단위로 프로그램이 실행되어 
Guest OS를 탑재하고 있는 VM보다는 덜 부담스러운 프로그램 체계 관리가 가능하다.

- 도커 컨테이너에서 돌아가는 애플리케이션은 컨테이너가 제공하는 격리 기능 내부에 샌드박스가 있지만, 여전히 같은 호스트의 다른 컨테이너와 동일한 커널 공유
(단, 한 호스트 위에서 올라가기 때문에 커널은 서로 공유하게 된다.)

![image](https://user-images.githubusercontent.com/12759500/229355768-8e07a629-978c-4c09-a141-6f3aa56142cc.png)
