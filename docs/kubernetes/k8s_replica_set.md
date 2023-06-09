---
layout: default
title: Kubernetes Replica_set
nav_order: 3
parent: Kubernetes 시작하기
grand_parent: Kubernetes
permalink: /docs/kubernetes/k8s_start/k8s_replica_set/
---

# 3. Replica Set에 대해

1. 레플리카 셋을 사용하는 이유

   ex) Nginx Pod 삭제

   ![Untitled](https://user-images.githubusercontent.com/12759500/230754766-ae8a8c64-5558-42e7-93c1-eb55fe7cebe2.png)

   - k8s에서는 기본 단위가 pod이므로 동일한 여러 개의 pod를 생성해 외부 요청을 각 pod에 분배하는 방식을 사용.
     ⇒ 그렇다면, 동일한 여러 개의 pod는 어떻게 생성할 수 있을까

   1. 방식 : YAML 작성시 “—-”를 구분자로 사용하여 여러 리소스 정의

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: my-nginx-pod-a
   spec:
     containers:
       - name: my-nginx-container
         image: nginx:latest
         ports:
           - containerPort: 80
             protocol: TCP

   ---
   apiVersion: v1
   kind: Pod
   metadata:
     name: my-nginx-pod-b
   spec:
     containers:
       - name: my-nginx-container
         image: nginx:latest
         ports:
           - containerPort: 80
             protocol: TCP
   ```

   ![Untitled 1](https://user-images.githubusercontent.com/12759500/230754770-03925990-dcbd-479b-bd2d-fcda9efc0d27.png)

   그러나, Pod만 YAML 파일에 정의해 사용하는 방식은 여러가지 한계점이 존재.
   따라서, k8s에서 pod만 정의해 사용하는 경우는 거의 없고, ReplicaSet라는 오브젝트를 사용.

   2. Replica Set 역할

   - 정해진 수의 동일 pod가 항상 실행되도록 관리
   - Node 장애 등의 이유로 pod 사용이 불가하다면, 다른 node에서 pod 재생성

   3. Replica Set 사용 예시

   ```yaml
   apiVersion: apps/v1
   kind: ReplicaSet
   metadata:
     name: replicaset-nginx
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: my-nginx-pods-label
     template:
       metadata:
         name: my-nginx-pod
         labels:
           app: my-nginx-pods-label
       spec:
         containers:
           - name: nginx
             image: nginx:latest
             ports:
               - containerPort: 80
   ```

   ![Untitled 2](https://user-images.githubusercontent.com/12759500/230754773-8736ca18-50a7-4d73-a25b-d04369a2c125.png)

   ![Untitled 3](https://user-images.githubusercontent.com/12759500/230754779-6acb7d99-2482-473b-b72c-dfe33f680a13.png)

   4. Replica Set 사용 예시 : app Label을 미리 생성해 놓을 경우

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: my-nginx-pod
     labels:
       app: my-nginx-pods-label
   spec:
     containers:
       - name: my-nginx-container
         image: nginx:latest
         ports:
           - containerPort: 80
   ```

   ![Untitled 4](https://user-images.githubusercontent.com/12759500/230754783-d6aba891-fefe-4d24-ac8b-05ed97238173.png)

   - 생성 화면

   ![Untitled 5](https://user-images.githubusercontent.com/12759500/230754788-913ee0a3-1885-4aed-8a9b-9a742b6eda1e.png)

   ⇒ replicaset-nginx.yaml에서 replica갯수가 3개이고,
   selector.matchLabel에 app:my-nginx-pods-label 라벨을 가지는 포드가 이미 1개 미리 존재하기 때문에, 실제로는 2개가 만들어 졌다

   ![Untitled 6](https://user-images.githubusercontent.com/12759500/230754796-2283fd5f-2272-464c-a020-61dcac4f5177.png)

   ⇒ 수동으로 pod를 삭제하고 나면, 위와 같이 새 pod를 생성한다

   ![Untitled 7](https://user-images.githubusercontent.com/12759500/230754798-bba1cb2f-7987-43a4-a99c-e821b0ac66fe.png)

   ⇒ 만약 label을 하나 지운다면, replica 갯수 3개 설정에 의해서 새로운 pod를 생성한다.

   ![Untitled 8](https://user-images.githubusercontent.com/12759500/230754804-651fa5d3-7d2d-41d0-b5ba-4a5abc60413c.png)

   ⇒ 따라서, <LABELS>가 none인 pod는 수동으로 삭제 해야 한다

   참고) 레플리케이션 컨트롤러 vs 레플리카셋

   - 이전 버전에서는 레플리케이션 컨트롤러를 사용하였음
   - “mtachExpression” 기반의 라벨 셀렉터를 사용할 수 있었음.

   ```yaml
   selector :
   	matchExpressions :
   		- key : app
   			values :
   				- my-nginx-pods-label
   				- your-nginx-pods-label
   			operator : In
   template :

   ```
