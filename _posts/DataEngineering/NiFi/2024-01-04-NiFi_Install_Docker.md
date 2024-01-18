---
title: NiFi_Install_Docker 
date: 2024-01-04 00:00:00 +00:00
categories: [DataEngineering, NiFi]
tags:
  [
    DataEngineering,
    NiFi
  ]
---

# Install Nifi with Docker

- Apache Nifi 설치 with Docker

1) docker에서 nifi 이미지를 내려받는다

```bash
$ docker pull apache/nifi:latest
```

2) nifi 컨테이너를 올리기 위한 docker-compose.yml 파일 작성

```bash
version: "3.9"
services :
  nifi:
    container_name : nifi
    image : apache/nifi:latest
    ports :
    - 8443:8443
    environment :
    - NIFI_WEB_HTTP_PORT=8443
    volumes :
      - vol-state:/opt/nifi/nifi-current/state
      - vol-db:/opt/nifi/nifi-current/database_repository
      - vol-flowfile:/opt/nifi/nifi-current/flowfile_repository
      - vol-content:/opt/nifi/nifi-current/content_repository
      - vol-provenance:/opt/nifi/nifi-current/provenance_repository
      - vol-logs:/opt/nifi/nifi-current/logs
      - vol-conf:/opt/nifi/nifi-current/conf
volumes :
  vol-state :
    external : true
  vol-db :
    external : true
  vol-flowfile :
    external : true
  vol-content :
    external : true
  vol-provenance :
    external : true
  vol-logs :
    external : true
  vol-conf :
    external : true
```

3) docker-compose.yml 파일 작동

```bash
$ docker-compose up -d
```

![Untitled](https://user-images.githubusercontent.com/12759500/229808603-bea7b14a-1e82-4452-9b11-d8c29c93c42e.png)

4) [localhost:8443/nifi](http://localhost:8443/nifi) 로 이동하여 Web UI 확인

![Untitled 1](https://user-images.githubusercontent.com/12759500/229808627-3b459d9a-422e-4f24-91e4-5799654322e1.png)

참고 : [https://www.youtube.com/watch?v=ArLijmbZUYk&ab_channel=TalkTechWithSantosh](https://www.youtube.com/watch?v=ArLijmbZUYk&ab_channel=TalkTechWithSantosh)

[Understanding Nifi UI](https://www.notion.so/Understanding-Nifi-UI-e733bacfe648470eaa5ba615c45148f4)

[Processor](https://www.notion.so/Processor-17af4c1d77cf4109a67dcd23accb74cc)
