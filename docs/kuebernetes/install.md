---
layout: default
title: Install k8s Cluster
nav_order: 1
parent: Kubernetes
---

# 1. 설치과정

# 0. 설치환경

- VirtualBox 3대
    - Master Node 1개, Worker Node 2개
- Memory : 4GB (Node 당)
- CPU : 2 Core (Node 당)
- OS : CentOS7

# 1. 공통 설정

1) yum 업데이트

```bash
[root@localhost] yum update -y
```

2) Selinux 설정

```bash
[root@localhost] vi /etc/sysconfig/selinux
SELINUX=permissive
[root@localhost] reboot
[root@localhost] getenforce
Permissive
```

3) 방화벽 해제

```bash
[root@localhost] systemctl stop firewalld && systemctl disable firewalld
```

4) NetworkManager 비활성화

```bash
[root@localhost] systemctl stop NetworkManager && systemctl disable NetworkManager
```

5) SWAP 비활성화

```bash
[root@localhost] swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab
```

6) Iptables 커널 옵션 활성화

```bash
# Centos7 사용 시 iptables가 무시되서 트래픽이 잘못 라우팅 되는 문제 발생 가능성 존재
[root@localhost] cat <<EOF >  /etc/sysctl.d/k8s.conf
> net.bridge.bridge-nf-call-ip6tables = 1
> net.bridge.bridge-nf-call-iptables = 1
> EOF
[root@localhost] sysctl --system
```

7) k8s Yum Repository 설정

```bash
[root@localhost] cat <<EOF > /etc/yum.repos.d/kubernetes.repo
> [kubernetes]
> name=Kubernetes
> baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
> enabled=1
> gpgcheck=1
> repo_gpgcheck=1
> gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
> EOF
```

8) 호스트 등록

```bash
[root@localhost] cat << EOF >> /etc/hosts
> k8s-master
> k8s-worker1
> k8s-worker2
> EOF
```

9) Docker 설치 전 사전 세팅

```bash
[root@localhost] yum install -y yum-utils device-mapper-persistent-data lvm2
```

10) Docker 저장소 설정

```bash
[root@localhost] yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

11) Docker Engine 설치

```bash
[root@localhost] sudo yum install -y docker-ce docker-ce-cli containerd.io
```

12) containerd 설정

```bash
[root@localhost] vi /etc/containerd/config.toml
disabled_plugins = ["cri"] # 주석처리
[root@localhost] sudo systemctl restart containerd
```

(미 설정 시 아래와 같은 에러 발생)

![Untitled](https://user-images.githubusercontent.com/12759500/230753939-5de6c9f0-5cd7-4595-af20-b0ab46a522c7.png)

13) k8s 설치

```bash
[root@localhost] yum install -y --disableexcludes=kubernetes kubeadm-1.15.5-0.x86\_64 kubectl-1.15.5-0.x86\_64 kubelet-1.15.5-0.x86\_64
```

14) VM 복제 진행

⇒ Master, Worker1, Worker2

15) Node별로 hostname 지정 및 /etc/hosts 등록

```bash
# Master Node
[root@localhost] vi /etc/hosts
192.168.56.106 k8s-master
192.168.56.108 k8s-worker1
192.168.56.107 k8s-worker2

[root@localhost] sshpass -p 'root' scp -o StrictHostKeyChecking=no /etc/hosts root@k8s-worker1:/etc/hosts
[root@localhost] sshpass -p 'root' scp -o StrictHostKeyChecking=no /etc/hosts root@k8s-worker2:/etc/hosts

# Master
[root@localhost] vi /etc/hostname
k8s-master
[root@localhost] reboot

# Worker1
[root@localhost] vi /etc/hostname
k8s-worker1
[root@localhost] reboot

# Worker2
[root@localhost] vi /etc/hostname
k8s-worker2
[root@localhost] reboot
```

# 2. Master node 설정

1) 도커 실행

```bash
[root@k8s-master] sudo systemctl daemon-reload
[root@k8s-master] sudo systemctl enable --now docker
```

2) 쿠버네티스 실행

```bash
[root@k8s-master] sudo systemctl enable --now kubelet
```

3) 쿠버네티스 초기화 실행

```bash
# --apiserver-advertise-address : 다른 노드가 마스터에게 접근할 수 있는 IP주소
# --pod-network-cidr : k8s에서 사용할 네트워크 대역
[root@k8s-master] kubeadm init \
--apiserver-advertise-address 192.168.56.106 \
--pod-network-cidr=192.168.0.0/16
```

![Untitled 1](https://user-images.githubusercontent.com/12759500/230753948-54a57537-8e9e-4e7d-8fc2-66997cb36248.png)

4) 환경변수 설정

```bash
[root@k8s-master] mkdir -p $HOME/.kube
[root@k8s-master] sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@k8s-master] sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

# 3. Worker node 설정

1) 도커 실행

```bash
[root@k8s-worker] sudo systemctl daemon-reload
[root@k8s-worker] sudo systemctl enable --now docker
```

2) 쿠버네티스 실행

```bash
[root@k8s-worker] sudo systemctl enable --now kubelet
```

3) Master Node에 Join

```bash
# --discovery-token-unsafe-skip-ca-verification
[root@k8s-worker] kubeadm join 192.168.56.106:6443 \
--token lqqhsp.ehkfcrfr1le97o2g \
--discovery-token-ca-cert-hash {sha256:~~}
```

(아래와 같이 에러가 발생해서, --discovery-token-unsafe-skip-ca-verification 옵션 추가 )

![Untitled 2](https://user-images.githubusercontent.com/12759500/230753952-b1150551-983e-438e-9860-1c90e2e023a0.png)

4) kubernetes 노드 상태 확인

```bash
[root@k8s-master] kubectl get nodes
```

![Untitled 3](https://user-images.githubusercontent.com/12759500/230753953-5580611b-ada1-4a41-8b87-44cbfc6001ff.png)

5) kubernetes에 대한 네트워크 상태 확인

```bash
[root@k8s-master] kubectl get pods --all-namespaces
```

![Untitled 4](https://user-images.githubusercontent.com/12759500/230753957-72a433ef-52fc-4de2-879c-a19e3b6c7cf4.png)

# 4. 네트워크 설정

```bash
[root@k8s-master] wget https://docs.projectcalico.org/v3.9/manifests/calico.yaml

# 아래 명령어는 kubectl init 당시 대역 변경이 172.16.0.0 대역으로 될때에만 할것
# [root@k8s-master] sed s/192.168.0.0\\/16/172.16.0.0\\/16/g -i calico.yaml

[root@k8s-master] kubectl apply -f calico.yaml
```

현재 아래와 같이 [apiextensions.k8s.io/v1beta1에서](http://apiextensions.k8s.io/v1beta1에서) 모듈을 못찾는 현상 발생

⇒ 재설치 후, 정상 작동. (kubernetes 버전을 1.15.1로 다운그레이드 하고 진행 시도)

![Untitled 5](https://user-images.githubusercontent.com/12759500/230753958-df92d5f0-367c-45df-9cae-4691bbdf5388.png)

```bash
[root@k8s-master ~] kubectl get pods --namespace kube-system
```

![Untitled 6](https://user-images.githubusercontent.com/12759500/230753959-eb926c24-1a07-4d2b-8698-790bf9365931.png)

```bash
[root@k8s-master ~] kubectl get nodes
```

![Untitled 7](https://user-images.githubusercontent.com/12759500/230753963-fb4ea43d-2861-4480-a6ca-b34e9237932f.png)

출처 : [https://zunoxi.tistory.com/42?category=950191](https://zunoxi.tistory.com/42?category=950191)

출처2 : 시작하세요! 도커/쿠버네티스 (위키북스)

- 이슈

Worker Node에서 kubectl get node 사용 시

“The connection to the server localhost:8080 was refused - did you specify the right host or port?”

![Untitled 8](https://user-images.githubusercontent.com/12759500/230753965-c6ae0d79-35d2-4a0a-8f8a-68584cb9d467.png)

- 조치

```bash
[root@k8s-worker ~] mkdir -p $HOME/.kube
[root@k8s-worker ~] scp root@k8s-master1:/etc/kubernetes/admin.conf $HOME/.kube/config
```

![Untitled 9](https://user-images.githubusercontent.com/12759500/230753969-c30f1d94-416b-47d0-95a5-c4680d9ae19d.png)
