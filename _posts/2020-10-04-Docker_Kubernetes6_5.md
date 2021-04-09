---
layout: post
title: MLOps Zero to Hero 8 - MicroK8s
tags: [MLOps]
use_math: true
---

# What is MicroK8s?

MicroK8s는 가볍고 안정적인 Kubernetes 배포판입니다. 로컬 환경에서 경량 다중 노드 Kubernetes 클러스터를 쉽게 배포합니다. 이런 특징덕에 디스크 및 메모리 공간이 작은 엔터프라이즈 급 솔루션에 적합합니다. MicroK8s는 기본 kubernetes 시스템에, Istio, Knative, Grafana, Cilium 등의 add-on(추가 기능)을 제공합니다.

<br>

## Why use it?

Kubernetes를 사용하려면 복잡한 배포를 설정하고 실행해야하는 어려움이 있습니다. 그러나 MicroK8s를 사용하면 60 초 이내에 시작할 수 있습니다.

###  소형

MicroK8s는 작으면서도 안정적입니다. 또한 Ubuntu에서 실행할 때 Azure AKS, Amazon EKS, Google GKE와 호환 가능합니다.

### 단순성

단순성과 확실성을 위해 snap 설치로 관리 및 운영을 최소화합니다.

### 보안

모든 보안 문제에 대한 업데이트를 사용할 수 있으며, 즉시 적용하거나 유지 관리주기에 맞게 예약 할 수 있습니다.

### 업데이트

MicroK8s는 최신 K8을 추적하거나 1.10 이후의 모든 릴리스 버전을 유지할 수 있습니다.

### 간편한 add-on 추가

MicroK8s는 간편하게 add-on을 추가 가능합니다.

> 다양한 add-on 추가 가능

![](https://assets.ubuntu.com/v1/cbe37336-MicroK8s-Apps.svg)



<br>

## 주요 기능

MicroK8s에는 기본 K8s 기능 및 서비스에 대한 add-on을 제공합니다.

### 서비스 메시

- Istio
- Linkerd

### 서버리스

- Knative

### 모니터링

- Fluentd
- Prometheus
- Grafana
- Metrics

### 업데이트

- 최신 Kubernetes 버전으로 자동 업데이트

### ML

- Kubeflow
- AI / ML 용 GPGPU 바인딩

<br>

# Microk8s vs Minikube

Minikube가 이미 이런 툴로 쓰이고 있지 않나요?

하지만 한 가지 중요한 차이점이 있습니다. Minikube는 일반적으로 Kubernetes 클러스터 용 VM(Virtual Machine)을 가동하지만 MicroK8s는 Linux 용으로 설계되었으며 VM이 필요하지 않습니다.

이 차이에는 장단점이 있습니다. 첫 번째는 크로스 플랫폼 이식성입니다. Minikube는 운영 체제간 이식 가능합니다. 하지만 MicroK8s는 Linux가 필요하며 snap을 지원하는 배포판에서만 사용 가능합니다. 

두번째는 자원입니다. MicroK8s는 VM을 가동 할 필요가 없기 때문에 더 적은 리소스를 사용합니다.

이 외에도 Minikube에 비해 MicroK8s는 손쉽게 설치 및 사용 가능하고, add-on 추가를 통해 손쉽게 통합 ML 환경을 구축할 수 있습니다.

<br>

# High availability

서비스가 꺼지지 않는것은 프로덕션 환경에선 필수입니다. 이를 위해 MicroK8s는 관리자 개입없는 HA(High Availability) 복원과 자가 치유 기능을 제공합니다.

> Self-healing HA cluster

<center><img src="https://assets.ubuntu.com/v1/9d785fad-Automatic+node+allocation.svg" width="70%"></center>

<br>

## HA Kubernetes?

HA Kubernetes 클러스터는 모든 구성 요소의 장애를 견딜 수 있으며 중단없이 워크로드를 계속 제공 합니다. 

HA Kubernetes를 위해선 둘 이상의 node가 필요합니다. 서비스는 두 개 이상의 노드에서 실행되며, 하나의 노드가 꺼져도, 다른 노드가 살아있어 서비스를 유지 할 수 있습니다.  MicroK8s 클러스터의 모든 노드는 API 서버로, 로드 밸런싱을 단순화하고 실패 할 경우 다른 API 엔드 포인트로 즉시 전환 할 수 있습니다.

<br>

## HA Kubernetes 클러스터

MicroK8s 노드를 더 추가하기 만하면 프로덕션 레벨의 Kubernetes 클러스터가 완성됩니다. 추가 구성은 필요하지 않습니다. 예를들어 세 대의 컴퓨터에 MicroK8s을 설치하고 join 명령을 실행하여 이들을 함께 연결하면, 즉시 HA가 활성화 된 프로덕션 레벨 Kubernetes 클러스터가 생성됩니다.

<br>

*HA (High Availability)*

*정보기술에서, HA란 긴 시간동안 지속적 운영이 가능한 시스템을 말합니다.* 

<br>

**참고 자료**

[microk8s](https://microk8s.io/)

[discuss](https://discuss.kubernetes.io/c/general-discussions/microk8s/26)

[medium](https://medium.com/faun/an-overview-of-microk8s-and-why-using-it-in-the-cloud-was-a-terrible-idea-9ba8506dc467)

[StackExchange](https://serverfault.com/questions/941857/is-microk8s-suitable-for-production-environments-or-is-it-just-for-development)

[linuxiac](https://linuxiac.com/microk8s-is-a-lightweight-production-ready-kubernetes-distribution/)