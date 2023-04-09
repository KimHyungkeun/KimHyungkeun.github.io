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

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled.png)

## 2. Deployment 사용 이유

- 어플리케이션 업데이트와 배포를 더욱 편하게 하기 위함
- 어플리케이션 업데이트 시, 변경 사항을 저장하는 revision을 남겨 롤백을 가능하게 해주고, 무중단 서비스를 위해 pod의 롤링 업데이트의 전략 지정도 가능

ex) record 옵션 추가 후 deployment 생성

```yaml
# deployment 생성
[root@k8s-master ~] kubectl apply -f deployment-nginx.yaml --record
```

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled%201.png)

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled%202.png)

```yaml
# image update
[root@k8s-master ~] kubectl set image deployment my-nginx-deployment nginx=nginx:1.11 --record
[root@k8s-master ~] kubectl get pods
```

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled%203.png)

```yaml
[root@k8s-master ~] kubectl get replicasets
```

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled%204.png)

⇒ 위와 같이 replicasets의 id가 새로 추가되었다

ex2) 버전 되돌리기

- revision history 탐색

```bash
[root@k8s-master ~] kubectl rollout history deployment my-nginx-deployment
```

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled%205.png)

- 버전 되돌리기

```bash
[root@k8s-master ~] kubectl rollout undo deployment my-nginx-deployment --to-revision=1
```

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled%206.png)

- 리소스 정보 확인

```bash
[root@k8s-master ~] kubectl describe deploy my-nginx-deployment
```

![Untitled](4)%20Deployment%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A2%202806208d842a435bbcf777b43bcf7ef4/Untitled%207.png)

⇒ NewReplicaSet : 9b5988dd로 되어있다