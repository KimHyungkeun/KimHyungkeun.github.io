---
layout: default
title: Kubernetes pod
nav_order: 2
parent: Kubernetes 시작하기
grand_parent: Kubernetes
permalink: /docs/kubernetes/k8s_start/k8s_pod/
---

# 2. Pod에 대해

## 1. Pod : 컨테이너를 다루는 기본 단위

1. 예시 : nginx-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx-pod
spec:
  containers:
    - name: my-nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
          protocol: TCP
```

- YAML 파일은 아래 구성으로 이루어져 있다
  - apiVersion : YAML 파일에서 정의한 오브젝트 API 버전
  - kind : 리소스의 종류. 현재는 Pod를 생성해야해서 Pod로 입력
    - 아래와 같이 api-resource 종류 확인 가능
    ![Untitled](https://user-images.githubusercontent.com/12759500/230754716-1423926e-263e-4145-bc57-9dacf32e9dec.png)
  - metadata : 라벨, 주석, 이름 등과 같은 리소스의 부가 정보를 입력
  - spec : 리소스를 생성하기 위한 자세한 정보를 입력. 위 파일에서는 컨테이너 정보를 정의하는 containers 항목을 작성한 뒤, 하위 항목인 image에서 docker 이미지를 지정

2. 실행

```bash
[root@k8s-master] kubectl apply -f nginx-pod.yaml
```

![Untitled 1](https://user-images.githubusercontent.com/12759500/230754719-44515591-ef73-49ea-8636-39293eaa3326.png)

3. pod 확인

```bash
[root@k8s-master] kubectl get pods
```

![Untitled 2](https://user-images.githubusercontent.com/12759500/230754722-78d9ff13-9d82-48a1-975f-8554a426bfe3.png)

4. pod 상세정보 확인

```bash
[root@k8s-master] kubectl describe pods my-nginx-pod
```

![Untitled 3](https://user-images.githubusercontent.com/12759500/230754727-1ce0d310-1bcb-4d94-9c34-bf080fea9853.png)

5. pod 컨테이너 내부 접속

```bash
[root@k8s-master] kubectl exec -it my-nginx-pod bash
```

![Untitled 4](https://user-images.githubusercontent.com/12759500/230754729-1908a80b-cc25-452d-a573-279962b60ca7.png)

6. pod 로그 확인

```bash
[root@k8s-master] kubectl logs my-nginx-pod
```

![Untitled 5](https://user-images.githubusercontent.com/12759500/230754731-8e559bab-c001-461c-86dd-607459ea0838.png)

7. pod 삭제

```bash
[root@k8s-master] kubectl delete -f nginx-pod.yaml
```

![Untitled 6](https://user-images.githubusercontent.com/12759500/230754735-c13ad1db-7cf7-4bad-a454-9ee4a8b8359b.png)

## 2. Pod vs Docker Container

1. Pod와 Container

```bash
[root@k8s-master] kubectl get pods
```

![Untitled 7](https://user-images.githubusercontent.com/12759500/230754739-a6bef32b-7844-4cbd-8bb9-a345b2ebcd89.png)

- Pod는 반드시 1개의 컨테이너가 되어야 하는 것은 아니다
- 위 처럼, 한 pod 안에 2개 이상의 컨테이너를 돌리는 것이 가능하다

2. 예시 : nginx-pod-with-ubuntu.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx-pod
spec:
  containers:
    - name: my-nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
          protocol: TCP

    - name: ubuntu-sidecar-container
      image: alicek106/rr-test:curl
      command: ["tail"]
      args: ["-f", "/dev/null"] # 컨테이너가 종료되지 않도록 유지
```

- bash 쉘 실행

![Untitled 8](https://user-images.githubusercontent.com/12759500/230754745-f7af6857-9afe-4060-9d71-06be5169b9a7.png)
