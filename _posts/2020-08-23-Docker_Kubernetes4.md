---
layout: post
title: MLOps 5 - Kubernetes 구성 요소
tags: [MLOps]
use_math: true
---

쿠버네티스의 구성요소는 크게 컴포넌트와 object가 있습니다.

# 쿠버네티스 컴포넌트

쿠버네티스 컴포넌트는 크게 **컨트롤 플레인**과 **노드**로 이루어져 있습니다.

<br>

## 컨트롤 플레인 컴포넌트

컨트롤 플레인은 클러스터에 대한 유지 및 제어를 하지만, 애플리케이션을 실행하진 않습니다. 컨트롤 플레인은 클러스터 내 어떤 머신에서든 동작 가능하지만, 보통 동일 머신 상에 모든 컨트롤 플레인을 구동시킵니다.

컨트롤 플레인은 다음 요소들로 구성됩니다.

> 컨트롤 플레인 (컴포넌트) 구성 요소

<center> <img src="https://user-images.githubusercontent.com/31475037/93835893-bdb41100-fcbb-11ea-8e44-9ddc76875db0.png"> </center>

### kube-apiserver

kube-apiserver는 쿠버네티스 API를 노출합니다. 

API 서버는 쿠버네티스 컨트롤 플레인의 프론트 엔드로서 사용자와 컨트롤 플레인 내의 요소들과 통신합니다. 또한 수평적(scale out)으로 확장가능케 설계되었습니다. 여러 kube-apiserver 인스턴스를 실행하고, 인스턴스간 트래픽을 조절합니다.

### etcd

모든 클러스터 데이터를 저장하는 분산 데이터 저장소(storage)로서 고가용성 key-value 저장소입니다.

### kube-scheduler

새로 생성된 pod에게 노드를 배정합니다. 이를 통해 pod이 애플리케이션을 실행할 수 있도록 배포합니다.

### kube-controller-manager

kube-controller-manager는 노드에 있는 컨트롤러를 관리하는 컴포넌트입니다. 현재 제공되는 컨트롤러는 다음과 같습니다.

- replication controller
- endpoints controller
- namespace controller
- serviceaccounts controller

<br>

## 노드

노드는 동작 중인 파드를 유지, 컨테이너화된 애플리케이션을 실행합니다.

> 노드 (컴포넌트)의 구성 요소

<center><img src="https://user-images.githubusercontent.com/31475037/93835878-b2f97c00-fcbb-11ea-8e47-98c5a1ab0ed6.png"></center>

### kubelet

각각의 노드에서 실행되며, 컨트롤 플레인의 kube-apiserver와 통신하고 파드에서 컨테이너가 동작하도록 관리합니다.

Kubelet은 다양한 앱 디스크립터를 통해 만들어진 Pod Spec을 받아 컨테이너가 해당 pod 스펙에 따라 동작하도록 합니다.

### kube-proxy

kube-proxy는 각 노드에서 실행되는 네트워크 프록시로, 쿠버네티스의 서비스 개념의 구현부입니다. kube-proxy는 노드의 네트워크 규칙을 관리하며, 주요기능으로서 애플리케이션 구성 요소간 네트워크 트래픽을 로드밸런싱 합니다. 즉, **pod에 요청이 들어왔을 때 어떤 Pod한테 요청을 전달할 것인가?**를 결정합니다.

### container runtime

컨테이너 실행을 담당하는 소프트웨어 입니다. 보통 docker를 씁니다.

<br>

# 쿠버네티스 오브젝트

오브젝트는 사용자가 쿠버네티스에 바라는 상태(desired state)를 의미하고 컨트롤러는 객체가 원래 설정된 상태를 유지 및 관리하는 역할을 합니다.  

오브젝트에는 pod, service, volume, namespace 등이 있습니다.

쿠버네티스 클러스터에 객체나 컨트롤러가 어떤 상태여야 하는지를 제출할때는 yaml 파일형식의 템플릿을 사용합니다. 

오브젝트를 중복 없이 식별하기 위해 쿠버네티스 시스템은 **UID**라는 문자열을 생성합니다. 쿠버네티스 클러스터가 구동되는 전체 시간에 걸쳐 생성되는 모든 오브젝트는 서로 구분되는 UID를 갖습니다.

<br>

## Pod

쿠버네티스 오브젝트 중에서 pod이 가장 중요합니다.

쿠버네티스는 개별 컨테이너들을 직접 다루지 않고, 함께 배치된 다수의 컨테이너를 pod이란 곳에 묶어 관리합니다. 각 **pod은 자체 IP, host name, process**이 있는 논리적으로 분리된 머신입니다. Pod은 프로세스를 실행하는 하나 이상의 컨테이너를 가지고 있습니다.

pod은 일시적이며 언제든 사라질 수 있습니다.

> pod의 구조

<center><img src="https://subicura.com/assets/article_images/2019-05-19-kubernetes-basic-1/pod.png" width="70%"></center>

<br>

## 레이블 & 네임스페이스

하나의 클러스터를 논리적으로 구분하여 사용할 수 있습니다.

### 레이블

레이블은 쿠버네티스 오브젝트를 계층화합니다.

키-값 쌍으로 이뤄진 레이블은 레이블 셀렉터를 사용해 리소스를 선택할 때 활용됩니다. 레이블은 오브젝트의 특성을 식별하는 데 사용되어 사용자에게 중요합니다. 오브젝트마다 키-값으로 레이블을 정의할 수 있으며 오브젝트의 키는 고유한 값이어야 합니다.

```yaml
"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

### 레이블 셀렉터

보통 많은 오브젝트에 중복되는 이름을 붙입니다. 레이블 셀렉터를 이용하면 중복된 이름을 사용할 수 있습니다.

### 네임스페이스

네임스페이스는 클러스터내 논리적 분리 단위입니다.

Pod, Service 등은 네임 스페이스 별로 생성이나 관리가 될 수 있고, 사용자의 권한 역시 이 네임 스페이스 별로 나눠서 부여할 수 있습니다. 즉 하나의 클러스터 내에, 개발/운영/테스트 환경을 분리하고자 할 때, 클러스터를 개발/운영/테스트 3개의 네임 스페이스로 나눠 운영할 수 있습니다.

네임스페이스는 다음 일들을 할 수 있습니다.

-  사용자별로 네임스페이스별 접근 권한을 다르게 운영할 수 있다.
-  네임스페이스별로 리소스의 할당량을 지정할 수 있다. 
- 네임 스페이스별로 리소스를 나눠서 관리할 수 있다. (Pod, Service 등)

> 네임스페이스

<center><img src="https://user-images.githubusercontent.com/31475037/94379543-10824280-016c-11eb-80dc-dd97bf903a2b.png" width="70%"></center>



<br>

## Service

우리가 pod을 만들게 되면, pod끼리는 내부에서 서로 접속이 가능하지만 외부에선 접속이 불가능합니다. 내부/외부에서 둘다 접속하고자 할 때는 service를 사용합니다. Pod에 Label을 붙이고, Label Selector로 선택하여 하나의 endpoint로 노출합니다. 

Service를 사용하면 크게 두가지 이점이 있습니다.

### IP, Port 관리

pod이 죽었을 때, pod은 레플리케이션컨트롤러에 의해 새로운 pod으로 대체됩니다. 새로운 pod은 새로운 IP와 port를 부여받습니다. 

### 단일 endpoint

Service가 생성되면 static ip를 할당받고 service가 존재하는 동안 변경되지 않습니다. pod에 직접 연결하는 대신 클라이언트는 service의 IP주소와 port를 통해 연결됩니다.

> Service 예시

<center><img src="https://user-images.githubusercontent.com/31475037/93834302-58a9ec80-fcb6-11ea-8b0c-12b058570027.png"></center>

<br>

## Storages

### Volume(임시적인 데이터 저장)

Pod 내부의 container 끼리는 서로 격리되어 있기에, 기본적으론 파일을 공유할 수 없습니다. 하지만 volume 설정을하면 container끼리 파일을 공유 할 수 있게 됩니다. Volume은 pod의 life-cycle과 동일하며, pod 삭제시 같이 삭제됩니다.

### Host Path

다음은 host path 라는 볼륨 타입인데, hostPath는 노드의 로컬 디스크의 경로를 Pod에서 마운트해서 사용하는 방식입니다. Pod이 삭제 되더라도 host path에 있는 파일들은 삭제되지 않습니다. 주의할점 중 하나는 Pod이 재시작되 다른 노드에서 기동될 경우, 그 노드의 hostPath를 사용하기 때문에, 이전에 다른 노드에서 사용한 hostPath의 파일 내용은 액세스가 불가능 하다는 점입니다. 

> host path예시

<center><img src="https://user-images.githubusercontent.com/31475037/94221675-8d23df80-ff26-11ea-9ae0-d1c99e1f2185.png"></center>

### Persistent Volume(영구적 데이터 저장)

컨테이너 내 저장되어 있는 파일은 임시적인 상태입니다. 만약 컨테이너가 꺼지고 삭제가 되면, 컨테이너 내부에 있던 파일들은 삭제가 됩니다. 

영구적인 파일 저장을 위해선 PV(Persistent Volume) 혹은 host path를 사용해야 합니다. 쿠버네티스 클러스터 관리자에 의해 제공된 저장소이며, volume과 유사하지만 pod과는 독립적인 life-cycle을 가집니다. 사용자가 용량, 모드 필요한 정보와 함께 PVC(Persistence Volume Claim)를 생성하면 이에 대응하는 PV가 생성됩니다.

그렇다면 host path대신 PV를 쓰면 어떤 이점이 있을까요?

일반적으로 디스크 볼륨을 설정하려면 물리적 디스크를 생성해야 하고, 이러한 물리적 디스크에 대한 설정 및 정보를 자세히 알아야 합니다. 그런데 쿠버네티스는 하드웨어 인프라 추상화를 통해 개발자가 손쉽게 필요한 인프라를 사용하도록 인프라와 개발을 분리 시켜줍니다. 그렇기에 host path 대신 PV를 쓰게 되면 물리적인 디스크에 대한 설정 및 지식 없이도 개발자가 하드웨어 인프라를 효율적으로 사용할 수 있게 합니다. 

사용법은 실제 물리 디스크를 PV라는 이름으로 쿠버네티스에 등록합니다. 개발자는 Pod을 생성할때 볼륨을 정의하고, 이 볼륨 정의 부분에 물리적 디스크에 대한 특성을 정의하는 것이 아니라 PVC를 지정하여, 인프라 관리자가 생성한 PV와 연결합니다.

> PV

![](https://t1.daumcdn.net/cfile/tistory/9997504A5B1D3F0F05)

<br>

## Controller

컨트롤러는 pod, storages, service, label 등의 기본 오브젝트들을 생성하고, 관리하는 역할을 합니다. 컨트롤러는 **ReplicaSet**, **Deployment**, DaemonSet, StatefulSet 등이 있습니다. 

### ReplicaSet

Pod을 여러 개(한 개 이상) 복제하여 관리하는 오브젝트입니다. Pod을 생성하고 개수를 유지하려면 반드시 ReplicaSet을 사용해야 합니다. ReplicaSet은 복제할 개수, 개수를 체크할 label selector, 생성할 Pod의 Spec 등을 가지고 있습니다. 

> ReplicaSet

<center><img src="https://subicura.com/assets/article_images/2019-05-19-kubernetes-basic-1/replicaset.png" width="80%"></center>

### Deployment

쿠버네티스에서 ReplicaSet을 이용하면 배포를 할 수 있긴 하지만, 굉장히 번거롭습니다. 이를 이용해 ReplicaSet을 버전별로 직접 배포 하는 대신, 간편하게 배포 할 수 있습니다. Deployment는 기본적으로 ReplicaSet를 생성하고, 이를 관리하는 역할을 합니다. 이를 통해 롤링 업데이트와 같은 상황에서 간편하게 배포 할 수 있습니다.

다음과 같이 ReplicaSet v1 버전을 배포했다가 ReplicaSet v2로 롤링 업데이트을 할 때, Deployment를 쓰면 간단히 할 수 있습니다.

> Deployment

<center><img src="https://user-images.githubusercontent.com/31475037/94380083-fb5ae300-016e-11eb-8c69-9c6bab3aeab2.png"></center>



### StatefulSet

Pod에 Identity(name, network id 등)를 고정시킵니다. 만약 pod이 죽었다해도 같은 상태로 띄웁니다.

### DaemonSet

모든 노드에 pod이 1개씩 실행되게하는 controller입니다.

<br>

# 요약

위에서 설명한 구성 요소들이 실제 동작하는 과정은 다음과 같습니다.

> 동작 과정

<center><img src="https://user-images.githubusercontent.com/31475037/94405060-f9167a00-01aa-11eb-9504-f949f4680f49.png"></center>



<br>

**참고 자료**

[쿠버네티스란 무엇인가?](https://kubernetes.io/ko/docs/concepts/overview/what-is-kubernetes/)

[k8s](https://kubernetes.io/ko/docs/concepts/overview/components/)

[subicura 블로그](https://subicura.com/2019/05/19/kubernetes-basic-1.html)

[조대협 블로그](https://bcho.tistory.com/1259)