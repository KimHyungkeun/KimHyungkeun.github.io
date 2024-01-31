---
title: BackTracking (백트래킹)
date: 2024-01-31 00:00:00 +00:00
categories: [CS, Algorithm]
tags:
  [
    CS,
    Algorithm,
    BackTracking
  ]
---

## 1. 백트래킹

- 모든 경우의 수를 탐색하며 최적해를 구하는 과정에서 유망하지 않은 쪽은 더 이상 구하지 않는 방법
- 용어
    - 유망(Promising) : 해가 될 가능서이 있는 경어 유망하다고 함
    - 가치치기 (Pruning) : 해가 될 가능성이 없는 경우 해당 노드를 제외
    - 백트래킹 (Backtracking) : 유망하지 않은 쪽으로 가지 않고 되돌아 오는

## 2. 백트래킹 예시

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/2784c7cb-50cb-4b85-be2c-667ffe0afbc6/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/8ebb28d3-0b1b-432f-b712-e84ed504b6bd/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/100520f1-8445-4851-91b0-8e7656b0bf9e/Untitled.png)