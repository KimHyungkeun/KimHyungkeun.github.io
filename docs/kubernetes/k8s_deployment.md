---
layout: default
title: Kubernetes deployment
nav_order: 4
parent: Kubernetes 시작하기
grand_parent: Kubernetes
permalink: /docs/kubernetes/k8s_start/k8s_deployment/
---

# 4) Deployment에 대해

## 1. Deployment 사용하기

```yaml
addVersion : apps/v1
kind : Deployment
metadata :
  name : my-nginx-deployment
spec :
  replicas : 3
  selector :
    matchLabels :
      app : my-nginx
  template :
    metadata :
      name : my-nginx-pod
      labels :
        app : my-nginx
    spec :
      containers :
      - name : nginx
        image : nginx:1.10
        ports :
        - containerPort : 80
```

![Untitled](https://user-images.githubusercontent.com/12759500/230754834-d3cb3fae-697f-4045-802f-b3386543ddc3.png)

## 2. Deployment 사용 이유

- 어플리케이션 업데이트와 배포를 더욱 편하게 하기 위함
- 어플리케이션 업데이트 시, 변경 사항을 저장하는 revision을 남겨 롤백을 가능하게 해주고, 무중단 서비스를 위해 pod의 롤링 업데이트의 전략 지정도 가능

ex) record 옵션 추가 후 deployment 생성

```yaml
# deployment 생성
[root@k8s-master ~] kubectl apply -f deployment-nginx.yaml --record
```

![Untitled 1](https://user-images.githubusercontent.com/12759500/230754838-9c9f09fe-5342-4dc3-9ba5-1a48cfd79e4d.png)

![Untitled 2](https://user-images.githubusercontent.com/12759500/230754841-b0f16835-8a1b-4f0a-a4b3-86df9d81e129.png)

```yaml
# image update
[root@k8s-master ~] kubectl set image deployment my-nginx-deployment nginx=nginx:1.11 --record
[root@k8s-master ~] kubectl get pods
```

![Untitled 3](https://user-images.githubusercontent.com/12759500/230754845-beac23f7-57de-4893-ab69-60af2e0939e1.png)

```yaml
[root@k8s-master ~] kubectl get replicasets
```

![Untitled 4](https://user-images.githubusercontent.com/12759500/230754847-f2652f9a-869d-44b9-a59a-ba17830a5760.png)

⇒ 위와 같이 replicasets의 id가 새로 추가되었다

ex2) 버전 되돌리기

- revision history 탐색

```bash
[root@k8s-master ~] kubectl rollout history deployment my-nginx-deployment
```

![Untitled 5](https://user-images.githubusercontent.com/12759500/230754851-c4030266-e899-47c0-9d0d-b2fa4c2ca954.png)

- 버전 되돌리기

```bash
[root@k8s-master ~] kubectl rollout undo deployment my-nginx-deployment --to-revision=1
```

![Untitled 6](https://user-images.githubusercontent.com/12759500/230754854-93fbef51-b67c-4ef7-b076-fa8ca1cbddb9.png)

- 리소스 정보 확인

```bash
[root@k8s-master ~] kubectl describe deploy my-nginx-deployment
```

![Untitled 7](https://user-images.githubusercontent.com/12759500/230754859-55e01b7c-d467-4e19-afdb-69b5fdadb213.png)

⇒ NewReplicaSet : 9b5988dd로 되어있다
