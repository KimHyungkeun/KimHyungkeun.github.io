---
layout: default
title: Kubernetes Service
nav_order: 5
parent: Kubernetes 시작하기
grand_parent: Kubernetes
permalink: /docs/kubernetes/k8s_start/k8s_service/
---

# 5. Service에 대해

# 1) Service 란

1. Service

- 기존 Docker는 -p 옵션을 통해 내부와 외부를 포트로써 연결
  ```bash
  ex) docker run -itd --name myapp -p 80:80 nginx:latest
  ```
- 그러나 k8s에서는 pod에 접근하도록 정의하는 방법이 docker와 좀 다르다
- deployment yaml에는 단지 pod의 app이 사용할 내부 port만 정의한다
- YAML 파일에 containerPort 항목을 정의했다고 해서 이 pod가 바로 외부로 노출되는 것은 아님
- 다른 deployment pod들이 내부적으로 접근하기 위해서는 service라고 부르는 별도 kubernetes 오브젝트를 생성해야 한다

2. Service의 기능

- 여러 개의 pod에 쉽게 접근할 수 있도록 고유한 도메인 이름을 부여
- 여러 개의 pod에 접근할 때, 요청을 분산하는 load balancer 기능을 수행
- 클라우드 플랫폼의 load balancer, 클러스터 노드 포트 등을 통해 pod를 외부로 노출

3. Service의 종류

1. Cluster IP 타입

- k8s 내부에서만 pod들에 접근할 때 사용
- 외부로 pod를 노출하지 않기 때문에 k8s 클러스터 내부에서만 사용되는 pod

2. NodePort 타입

- pod에 접근할 수 있는 포트를 클러스터의 모든 노드에 동일하게 개방
- 따라서, 외부에서 pod에 접근할 수 있는 서비스 타입
- 접근 가능한 포트는 랜덤으로 정해지나, 특정 포트로 접근하도록 설정 가능

3. LoadBalancer 타입

- 클라우드 플랫폼에서 제공하는 load balancer를 동적으로 프로비저닝 하여 pod 연결
- NodePort 타입과 마찬가지로 외부에서 pod에 접근 가능
- 그러나 AWS, GCP 등과 같은 클라우드 플랫폼 환경에서만 사용 가능

4. 예시코드

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hostname-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webserver
  template:
    metadata:
      name: my-webserver
      labels:
        app: webserver
    spec:
      containers:
        - name: my-webserver
          image: alicek106/rr-test:echo-hostname
          ports:
            - containerPort: 80
```

# 2) ClusterIP 타입

```yaml
# ex) hostname-svc-clusterip.yaml 생성
apiVersion: v1
kind: Service
metadata:
  name: hostname-svc-clusterip
spec:
  ports:
    - name: web-port
      port: 8080
      targetPort: 80
  selector:
    app: webserver
  type: ClusterIP
```

- spec.selector
  - 이 서비스에서 어떠한 라벨을 가지는 pod에 접근할 수 있게 할 것인지 결정
  - 위 코드에서는 app:webserver라는 라벨을 가지는 pod들의 집합에 접근 가능한 서비스 생성
- spec.ports.port
  - 생성된 서비스는 k8s 내부에서만 사용 가능한 고유 IP를 할당 받음
- spec.ports.targetPort
  - selector 항목에서 정의한 라벨에 의해 접근 대상이 된 pod들이 내부적으로 사용하고 있는 포트를 입력
  - 위 코드의 경우 targetPort 항목을 80으로 설정 하였음
- spec.type

  - 어떤 타입의 서비스인지 나타냄
  - ClusterIP, NodePort, LoadBalancer 타입 3가지로 나뉨

- 아래와 같이 service를 실행

```yaml
[k8s-master ~] kubectl apply -f hostname-svc-clusterip.yaml
service/hostname-svc-clusterip created
```

![Untitled](https://user-images.githubusercontent.com/12759500/231144462-1c021662-d2b3-465e-81f9-b58ee210afa0.png)

- pod목록을 조회

![Untitled 1](https://user-images.githubusercontent.com/12759500/231144526-9dda432f-c5c0-4425-969a-3d0a45491ee0.png)

- 임의로 pod를 하나 생성 후, service에 접근시도
- {서비스IP}:{Port} 또는 {pod이름}:{Port}로 접근
- 아래와 같이 서비스와 연결된 여러 개의 pod에 자동으로 요청이 분산되고 있음

```bash
[root@k8s-master chapter6] kubectl run -i --tty --rm debug --image=alicek106/ubuntu:curl --restart=Never -- bash
```

![Untitled 2](https://user-images.githubusercontent.com/12759500/231144589-3a580de1-446f-4f37-ac8a-ba8cfdae70c7.png)

![Untitled 3](https://user-images.githubusercontent.com/12759500/231144623-49682a04-057b-4355-a438-ae8d49296aee.png)

![Untitled 4](https://user-images.githubusercontent.com/12759500/231144653-ac45b1cd-f96e-4206-b6eb-a6d5d6c6f190.png)

![Untitled 5](https://user-images.githubusercontent.com/12759500/231144679-c00a89d9-6763-4f1d-a44e-a2f9ace9223e.png)

- 삭제 시 에는 아래와 같이 진행

```bash
[root@k8s-master chapter6] kubectl delete svc hostname-svc-clusterip
[root@k8s-master chapter6] kubectl delete -f hostname-svc-clusterip.yaml
```

- ClusterIP 타입 서비스 구조

![ClusterIP](https://user-images.githubusercontent.com/12759500/231144730-f6092a1f-d142-49d4-8465-7409cb2e1b01.jpg)

# 3) NodePort 타입

- ClusterIP 타입의 서비스는 내부에서만 접근 가능하나, NodePort 타입의 서비스는 클러스터 외부에서 접근 가능

```yaml
# hostname-svc-nodeport.yaml
apiVersion: v1
kind: Service
metadata:
  name: hostname-svc-nodeport
spec:
  ports:
    - name: web-port
      port: 8080
      targetPort: 80
  selector:
    app: webserver
  type: NodePort
```

- 아래와 같이 YAML 파일을 이용해 NodePort 타입의 서비스를 생성

```yaml
[root@k8s-master] kubectl apply -f k8s_workspace/chapter6/hostname-svc-nodeport.yaml
service/hostname-svc-nodeport created
```

![Untitled](https://user-images.githubusercontent.com/12759500/231146577-27784ed2-145c-4448-a79c-a5756d9c35e3.png)

- 노드 확인

```bash
[root@k8s-master] kubectl get nodes -o wide
```

![Untitled 1](https://user-images.githubusercontent.com/12759500/231146618-6be1bb21-a16a-4354-a18d-635d64ed111f.png)

![Untitled 2](https://user-images.githubusercontent.com/12759500/231146676-b6a6b49b-c64d-4043-afd4-b6f3052f920a.png)

- 주목할 점
  - NodePort 타입의 서비스인데도 kubectl get service 명령어에서는 CLUSTER-IP에 내부 IP가 할당
  - NodePort 타입의 서비스가 ClusterIP의 기능을 포함하고 있기 때문
  - NodePort 타입의 서비스를 생성하면 자동으로 ClusterIP의 기능 사용이 가능하므로, k8s 클러스터에서 서비스의 내부 IP와 DNS 이름을 사용해 접근 가능

```bash
# 아래와 같이 임시 pod를 만들고 curl 적용
[root@k8s-master chapter6] kubectl run -i --tty --rm debug --image=alicek106/ubuntu:curl --restart=Never -- bash
root@debug:/ curl 10.105.219.92:8080 --silent | grep Hello
	<p>Hello,  hostname-deployment-6cd58767b4-vx5jd</p>     </blockquote>
root@debug:/ curl hostname-svc-nodeport:8080 --silent | grep Hello
	<p>Hello,  hostname-deployment-6cd58767b4-vx5jd</p>     </blockquote>
```

![Untitled 3](https://user-images.githubusercontent.com/12759500/231146734-e5e174bd-0e0a-4158-9fa1-b5bd65cf5e4e.png)

- NodePort 구조
  - 외부에서 pod에 접근하기 위해 각 node에 개방된 포트로 요청을 전송
  - 예를 들어, 32168 port로 들어온 요청은 서비스와 연결된 pod 중 하나로 라우팅
  - 클러스터 내부에서는 ClusterIP 타입의 서비스와 동일하게 접근

![NodePort](https://user-images.githubusercontent.com/12759500/231146774-be7f4b29-2914-4c61-ae24-02a798cfb2ec.jpg)

- 유의점
  - 실제 운영환경에서 NodePort로 서비스를 외부에 제공하는 경우는 많지 않음
  - NodePort에서 80 또는 443으로 설정하기는 적절치 않으며, SSL 인증서 적용, 라우팅 등과 같은 복잡한 설정을 서비스에 적용하기가 어렵기 때문
  - 따라서, NodePort 서비스 자체보다는 **인그레스(Ingress)**라고 부르는 오브젝트 사용

# 4) LoadBalancer 타입 (추후진행)

- 서비스 생성과 동시에 로드 밸런서를 새롭게 생성해 pod와 연결
- NodePort와 LoadBalancer 타입의 차이
  - NodePort는 각 노드의 IP를 알아야만 pod에 접근
  - LoadBalancer 타입의 서비스는 클라우드 플랫폼으로부터 도메인 이름과 IP를 할당 받기 때문에 NodePort보다 더욱 쉽게 pod 접근 가능
- 단, LoadBalancer 타입의 서비스는 로드 밸런서를 동적으로 생성하는 기능을 제공하는 환경에서만 사용 가능
  - 일반적으로 AWS, GCP 등과 같은 클라우드 플랫폼 환경에서만 LoadBalancer 타입 사용이 가능하며, 가상머신이나 On-premise 환경에서는 사용하기 어려울 수 있다
- (추후 AWS 설치를 통해 다시 확인 예정)

# 5) externalTrafficPolicy 타입

- LoadBalancer 타입의 서비스를 사용하면 외부로부터 들어온 요청은 각 노드 중 하나로 전송
- 그 노드에서 다시 pod 중 하나로 전달된다
- NodePort 타입 또한 각 Node로 들어오는 요청은 다시 pod 중 하나로 전달

- 문제점
  - 아래와 같이 Worker A로 들어오는 요청은 Pod a나 Pod b 둘 중 하나로 전달될 예정
  - 그런데, A로 들어와도 Pod b로 전달되어 처리되면 불필요한 네트워크 hob이 발생
  - 게다가 노드 간의 리다이렉트가 발생하여, 트래픽의 출발지 주소가 바뀌는 SNAT 발생
    ⇒ 클라이언트 IP 주소 또한 보존되지 않음

![externalPolicy](https://user-images.githubusercontent.com/12759500/232005475-ab0b32a7-de9f-45a1-9ff4-55a12b092d3d.jpg)

- 아래 명령어를 통해 externalTrafficPolicy 상태 확인 : **Cluster 모드**

```bash
[root@k8s-master ~] kubectl get svc hostname-svc-nodeport -o yaml
-> externalTrafficPolicy가 Cluster로 되어있음
```

![Untitled](https://user-images.githubusercontent.com/12759500/232005590-d29ac86b-c9df-4a33-bcf2-0f65e118a6b1.png)

- 아래 service를 띄운 후, externalTrafficPolicy를 Local로 변경

  1. service 실행

  ```yaml
  # hostname-svc-lb-local.yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: hostname-svc-lb-local
  spec:
    externalTrafficPolicy: Local
    ports:
      - name: web-port
        port: 80
        targetPort: 80
    selector:
      app: webserver
    type: LoadBalancer
  ```

  ```bash
  # service 실행
  [root@k8s-master ~] kubectl apply -f k8s_workspace/chapter6/hostname-svc-lb-local.yaml
  service/hostname-svc-lb-local created
  ```

  2. hostname-deployment에서 pod 갯수를 1개로 감소 시킴

  ```bash
  [root@k8s-master ~] kubectl scale --replicas=1 deployment hostname-deployment
  deployment.extensions/hostname-deployment scaled
  [root@k8s-master ~] kubectl get pods -o wide
  => Node가 worker1에서만 서비스 중
  ```

  ![Untitled 1](https://user-images.githubusercontent.com/12759500/232005652-d4c9d868-8b3b-426a-b428-69ea601750e2.png)

  ![Untitled 2](https://user-images.githubusercontent.com/12759500/232005680-505c0625-a627-4543-a2d7-1fd662c3ca25.png)

  3. worker1에서 응답확인

  ```bash
  [root@k8s-worker1 ~] curl 192.168.56.109:31875 --silent | grep Hello
  ^C
  [root@k8s-worker1 ~] curl 192.168.56.110:31875 --silent | grep Hello
          <p>Hello,  hostname-deployment-6cd58767b4-5qztn</p>     </blockquote>
  [root@k8s-worker1 ~] curl 192.168.56.111:31875 --silent | grep Hello
  ^C

  # worker1인 56.110에서만 응답 확인
  ```

  ![Untitled 3](https://user-images.githubusercontent.com/12759500/232005722-7779a577-9a85-4a93-9bad-91a29c3f6fb8.png)

- ExternalTrafficPolicy 값에 따른 트래픽 흐름

  ![externalPolicyFlow](https://user-images.githubusercontent.com/12759500/232005815-ce21a789-402a-407a-a422-a9437e74b837.jpg)

- 가정 : 그렇다면 Local 설정이 무조건 효율적인가?

  ⇒ 결론적으로는 pod가 고르지않게 스케쥴링 될 경우, 요청이 고르게 분산되지 않을 수 있음

  ![externalPolicyLB](https://user-images.githubusercontent.com/12759500/232005851-707f164b-ec97-4c24-8d7e-7555430366b0.jpg)

  - 위 그림에서는 로드밸런서가 2개의 노드에 대해 트래픽을 절반씩 분배
  - 그러나, 각 pod가 실제로 받는 부하의 양은 동일하지 않음
  - 즉, 특정 노드의 pod에 부하가 집중되거나 적어질 수 있으며, 이는 곧 자원 활용(utilization) 측면에서 바람직하지 않을 수 있다.

- 결론
  - Cluster와 Local 둘 다 장단점이 있기 때문에 뚜렷한 정답은 없음
  - 불필요한 네트워크 hob으로 인한 latency나 client의 IP 보존이 중요하지 않다면 Cluster 모드
  - hob으로 인한 latency나 client IP 보존이 중요하다면 Local 모드

# 6) ExternalName 타입

- k8s를 외부 시스템과 연동할 경우 ExternalName 타입의 서비스 사용 가능
- ExternalName 타입을 사용해 service를 생성하면 service가 외부 도메인을 가리키도록 함
- 예시

```yaml
# external-svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: externalname-svc
spec:
  type: Externalname
  externalname: my.database.com
```

- service 실행 후, 목록 확인

```bash
[root@k8s-master ~] kubectl apply -f k8s_workspace/chapter6/external-svc.yaml
service/externalname-svc created
[root@k8s-master ~] kubectl get svc
NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)          AGE
externalname-svc        ExternalName   <none>          my.database.com   <none>           7s
```

![Untitled](https://user-images.githubusercontent.com/12759500/232005406-4cedbf5f-0be7-49a2-89a8-26a313fe5b38.png)
