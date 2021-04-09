---
layout: post
title: MLOps Zero to Hero 6 - Kubernetes 동작 방식
tags: [MLOps]
use_math: true
---

# 백그라운드에서 일어난 동작 이해하기

이전 포스트들에서 쿠버네티스의 개념과 구성 요소들에 대해 알아보았는데요, 그렇다면 쿠버네티스는 어떻게 동작할까요?

<br>

## 쿠버네티스의 작동방식

쿠버네티스에서 가장 중요한 것은 **desired state** 라는 개념입니다. Desired state란 개발자가 원하는 상태를 의미하며, 구체적으론 애플리케이션의 종류, 스펙, 몇 번 포트로 서비스하기를 원하는지 등을 의미합니다.

쿠버네티스는 **current state**를 모니터링하면서 관리자가 설정한 **desired state**를 유지하려 합니다. 그렇기에 개발자가 서버를 배포할 때 직접적인 명령어를 보내지 않고, 상태를 기술하는 방식을 사용합니다. 대표적으론 yaml 파일로 기술된 앱 디스크립션 파일이 있습니다.

![](https://subicura.com/assets/article_images/2019-05-19-kubernetes-basic-1/desired-state.png)



## 쿠버네티스 기술하기

개발자가 먼저 해야할 것은 앱에대한 정보를 기술하는 것입니다. 왜냐면 쿠버네티스가 오브젝트를 생성할 때, 오브젝트에 대한 기본 정보와 더불어, desired state를 기술한 오브젝트 spec이 필요합니다.

이러한 환경 변수나 설정값, 오브젝트 spec들을 변수로 관리, pod가 생성될때 이 값을 넣어줄 수 있는데, 이러한 기능을 제공하는 것이 바로 **configmap**과 **secret**입니다. 

### ConfigMap

configmap은 키/밸류 형식으로 저장이됩니다.

다음은 가장 간단한 예제입니다.

```yaml
apiVersion: v1              # 쿠버네티스 버전
kind: Pod                   # 오브젝트 종류
metadata:
    name: chacha-simple     # pod 이름
spec:
    containers:
    - image: chacha/exp     # 컨테이너 이미지
      name: cha             # 생성될 컨테이너 이름
      ports:
      - containerPort: 8080 # 컨테이너 포트
        protocol: TCP
      
status:
    conditions:
    - status: "True"
      type: Ready
    containerStatuses:
    - containerID: docker://f03234
      image: chacha/exp
      imageID: docker://43cfsc32
      name: cha
      ready: True
      restartCount: 0
      state:
          running: 
                  startedAt: 2020-09-18T12:46:05z
    hostIP: 10.132.0.4
    phase: Running
    podIP: 10.0.2.3
    startTime: 2020-09-18-T12:46:05z
```

**Metadata**: 이름, 네임스페이스, 레이블, 파드에 대한 정보를 기술

**Spec**: pod, 컨테이너, 볼륨, 기타 데이터에 대해 기술(**desired status**)

**Status**: pod 상태, 컨테이너에대한 설명과 상태, pod 내부 IP 등 현재 실행 중인 pod에 관한 현재 정보(**current state**) 

쿠버네티스 deployment는 클러스터에서 동작하는 애플리케이션을 표현해줄 수 있는 오브젝트입니다. Deployment를 생성할 때, spec에 3개의 레플리카가 동작되도록 설정할 수 있습니다. 쿠버네티스 시스템은 spec을 읽어, spec에 일치되도록 상태를 업데이트하여 3개의 의도한 애플리케이션 인스턴스를 구동합니다. 

만약, 그 인스턴스들 중 어느 하나가 문제로 인해 멈춘다면(상태 변화 발생), 쿠버네티스 시스템은 새 pod 생성을 통해 desired status(spec)와 current status(status)간의 차이에 대응합니다.

### Secret

Configmap이 일반적인 환경 설정 정보를 가지고 있따면, 패스워드, API 키와 같이 정보가 중요한 정보는 secret에 저장합니다. Secret은 저장된 내용을 지키기 위해서 추가 보안 기능을 제공합니다. 예를 들어 API server나 node의 파일에는 저장되지 않고, 항상 메모리에 저장되어 있기 때문에 상대적으로 접근이 어렵습니다.

하나의 secret 사이즈는 최대 1Mb까지 지원되는데, 메모리에 저장되는 특성 때문에, secret을 여러개 저장하게 되면 API Server나 노드에서 이를 저장하는 kubelet의 메모리 사용량이 늘어나서 Out Of Memory와 같은 이슈를 유발할 수 있기 때문에, 보안적으로 꼭 필요한 정보만 secret에 저장하도록 하는게 좋습니다. 

### 전달

Configmap이나 secret에 기술된 정보를 pod에 넘기는 방법은 두가지가 있습니다.

- 정의해놓은 값을 pod의 환경 변수 (Environment variable)로 넘기는 방법
- 정의해놓은 값을 pod의 디스크 볼륨으로 마운트 하는 방법

<br>

# 전체 파이프라인

쿠버네티스의 전체 파이프라인은 다음과 같습니다.

> 전체 파이프라인

![](https://user-images.githubusercontent.com/31475037/114015933-3fa90780-98a5-11eb-90fc-4f5835cccfca.png)

### 1. YAML로 작성

Yaml 혹은 Json 파일에 ConfigMap과 Secret을 정의합니다.

### 2. 이미지 push

쿠버네티스에서 애플리케이션을 실행하려면, 애플리케이션을 docker image로 만들어 도커 허브에 push해야 합니다.

### 3. kubectl에게 명령

Kubectl 명령어를 실행하면 kube-apiserver에 REST API로 yaml 혹은 json으로 작성된 파일이 전송됩니다. kubectl이 kube-apiserver에 yaml 파일 전송시, JSON 형식으로 정보를 변환시켜 전송합니다.

### 4. kubectl이 kube-apiserver 호출

Kube-apiserver가 yaml 파일을 읽고난뒤, kube-apiserver는 kube-scheduler에게 각 컨테이너에 필요한 리소스를 계산하라고 명령을 내립니다. 

### 5. 스케쥴링

Kube-scheduler는 사용 가능한 노드에 pod을 할당합니다. 

### 6. 노드에 명령

kube-apiserver가 노드의 kubelet에게 명령합니다. 해당 노드의 kubelet은 pod이 스케쥴링 됐다는 것을 전달받습니다.

### 7. kubelet은 도커에 이미지 실행하라 지시

kubelet이 도커(container runtime)에게 이미지를 실행하라 명령합니다.

### 7-1 일반적인 생성, 배포 방식

위와 같이 kubelet이 직접 도커에 명령을 내려 이미지를 실행할 수도 있지만, 매우 번거럽고 불편합니다. 특히나 롤링 업데이트와 같은 작업을 할 경우 일일히 다 배포해 줘야 하기 때문에 굉장히 괴롭습니다. 그래서 kubelet이 docker에게 명령을 내리는 것이 아닌 **kube-controller manager -> controller -> deployment -> replicaset**을 이용해 pod을 생성, 관리, 배포 합니다.

> 보통 이 방식으로 배포됨

![](https://user-images.githubusercontent.com/31475037/94405060-f9167a00-01aa-11eb-9504-f949f4680f49.png)

### 8. 도커 허브에서 이미지 pull

도커 이미지가 해당 노드 상에 없다면, 도커 허브에서 이미지를 pull해 옵니다. 이미지를 pull해온 후 도커는 컨테이너를 생성 및 실행시킵니다.

### 9. expose

위의 과정을 통해 생성된 pod들은 service 오브젝트에의해 외부로 노출됩니다.

또한 service와 함께 ingress를 controller를 같이 사용하여 효과적인 클러스터 관리를 가능케 합니다.(클라우드를 같이 사용하는 경우 loadbalancing을 같이 사용)

<br>

**참고 자료**

[쿠버네티스란 무엇인가?](https://kubernetes.io/ko/docs/concepts/overview/what-is-kubernetes/)

[subicura 블로그](https://subicura.com/2019/05/19/kubernetes-basic-1.html)