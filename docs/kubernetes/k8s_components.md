---
layout: default
title: Kubernetes 구성요소
nav_order: 1
parent: Kubernetes 시작하기
grand_parent: Kubernetes
permalink: /docs/kubernetes/k8s_start/k8s_components/
---

# 1) Kubernetes 구성 요소

## 1. 쿠버네티스 오브젝트

1) Pods : 컨테이너의 집합

2) Replica Set : 컨테이너의 집합을 관리하는 컨트롤러

3) Service Account : 사용자

4) Node : 노드

## 2. 쿠버네티스 컴포넌트

ex) 마스터노드 기준

- API 서버 (kube-apiserver)
- 컨트롤러 매니저 (kube-controller-manager)
- 스케쥴러 (kube-scheduler)
- DNS 서버 (coreDNS)
- 프록시 (kube-proxy)
- 네트워크 플러그인 (calico, flannel)

![Untitled](https://user-images.githubusercontent.com/12759500/230754708-52bd21a7-1a09-4d05-a821-86995b6049ba.png)

- kubenetes 클러스터에서는 kubelet이라는 agent가 모든 노드에서 실행된다
- 컨테이너의 생성, 삭제 뿐만 아니라 master와 worker 노드 간의 통신 역할을 함께 담당

유의점)

- kubernetes는 Docker에 내장된 기능이 아니고, 오히려 컨테이너 사용을 위해 Kubernetes가 Docker를 이용하는 방식
- 따라서, Kubernetes에서 반드시 Docker를 사용해야 하는 것은 아니다
- OCI(Open Container Initiative)라는 컨테이너의 런타임 표준을 구현한 CRI (Container Runtime Interface)를 갖추면 어떠한 컨테이너를 사용해도 문제 없음
