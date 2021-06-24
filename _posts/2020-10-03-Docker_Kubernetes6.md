---
layout: post
title: MLOps 7 - Service Mesh와 Knative
tags: [MLOps]
use_math: true
---

# Service Mesh

Service Mesh를 이해하기 위해선 MSA(Micro Service Architecture)의 장단점에 대해 알아야 합니다. MSA는 장점도 많지만, 단점도 많습니다. 

MSA는 기능을 서비스 단위로 잘게 나누다 보니, 시스템이 커지면 커질수록 서비스간 관리가 힘들어집니다. 특히, 서비스간 전체적 연결 구조 파악이 어려워, 이로 인한 장애가 났을때 어느 서비스에서 장애가 났는지 추적이 어려워집니다. 이런 MSA의 단점을 개선하기 위해 service mesh 기술이 나왔습니다. 

Service mesh를 이해하기 위해선 다음 개념들을 알아야 합니다.

<br>

## 프록시 

프록시란 '**대리**'라는 의미로 네트워크 기술에서는 중요한 개념이며, 보통 서버의 대라자 역할을 하는 서버를 의미합니다.

예를들어 아래와 같이 서비스와 서비스간의 호출이 있을때

> service간 호출

<center><img src="https://user-images.githubusercontent.com/31475037/95031529-679a9100-06f1-11eb-8c8b-dc3d6d9ff5ef.png"></center>



서비스 A가 서비스 B를 직접 호출 하는 것이 아니라 서비스 마다 프록시를 넣어 간접적으로 호출하게 됩니다.

> service에 proxy를 추가

<center><img src="https://user-images.githubusercontent.com/31475037/95031528-66696400-06f1-11eb-8223-ec5937ba4d70.png"></center>



<br>

## Sidecar pattern

MSA의 등장으로 개발 패러다임이 바뀌는 가운데 여러 design pattern이 나왔습니다. 그 중 sidcar pattern은 kubernetes에선 **하나의 pod에는 하나의 책임만 가지고 있어야 한다**의 원칙을 가지고 설계 되었습니다. 하지만 이런 패러다임 대신 log 수집기와 같은 메인 container를 보조하는 sidecar pattern이 등장했습니다. Sidecar pattern은 pod 안 메인 컨테이너를 **확장**해줍니다. 

> Sidecar pattern

<center><img src="https://user-images.githubusercontent.com/31475037/95032005-cd881800-06f3-11eb-966a-49f57db479e8.png"></center>

<br>

## Service Mesh

Service mesh는 소프트웨어 계층이 아니라 인프라 측면에서 MSA의 문제를 해결하기 위해 나왔습니다.

기존 대다수 Proxy 기술들은 서비스 트래픽을 네트워크 단(**L3, L4 layer**)에서 통제했습니다. 그러나 네트워크 기반(L3, L4 layer)의 proxy로는 MSA 구조에선 기능이 부족했습니다. 특히나 MSA가 커졌을 때, 서비스들은 네트워크를 통해 더 많은 통신이 필요 했고, 이를 load balancing 해줄 기능이 필요했습니다.

이렇게 커져가는 MSA 패러다임 속에서 **L7 layer proxy** 기능이 추가된 envoy proxy가 나왔습니다.

> 커져가는 MSA 

![](https://www.abhishek-tiwari.com/assets/images/Network-of-Microservices.jpg)

<br>

## Envoy proxy

Envoy proxy는 MSA를 위해 설계된 고성능 분산 c++ 프록시입니다. 기존의 **L3/L4 layer proxy**를 **L7 layer proxy** 까지 확장시켰습니다. 

다음 기능들을 지원합니다.

### 고성능 및 독립적

Envoy proxy는 c++로 구현된 메모리 사용량이 적은 고성능 서버입니다.

특정 플랫폼에 종속되지 않습니다. 이로인해 모든 프로그래밍 언어, 프레임워크와 함께 실행될 수 있으며 네트워크 인프라를 추상화 시킵니다.

### L7 layer proxy

L3/L4/L7 layer proxy를 사용합니다. L7 layer에서 HTTP,  gRPC, WebSocket 및 TCP 트래픽에 대한 라우팅 규칙을 사용하여 보다 세분화된 트래픽 관리를 합니다.

### 보안 및 인증

보안 정책을 시행하고 구성 API를 통해 정의된 액세스 제어 및 속도를 제한합니다.

그 외에도 **Dynamic service discovery**, **L7 layer Load balancing**, **HTTP/2, gRPC**를 지원합니다.

<br>

# Istio(이스티오)

Service mesh의 기본 아키텍처는 서비스끼리 직접통신하는 것이 아닌, 사이드카 패턴의 프록시를 배치하여 통신합니다. 이런 service mesh 기술의 대표가 **Istio**입니다. 

그러나 프록시를 사용해 트래픽을 통제하는 것은 장점이자 단점입니다. 서비스 수가 증가함에 따라 **프록시 수도 증가**하기 때문입니다. 이 문제를 해결하기 위해 각 프록시에 대한 설정정보를 **중앙 집중화된 컨트롤러**가 통제할 수 있게 설계되었습니다. 여기서 프록시로 envoy 프록시를 사용합니다.

<br>

## Istio Architecture

Istio의 구조는 크게 두가지 components로 이뤄져 있습니다. **Data plane**으로 envoy proxy, **control plane**으로 Istiod를 사용합니다.

### Istiod

Istiod는 service discovery, configuration(설정), certificatie(인증)를 관리 합니다. 

Istiod는 다음 components로 구성됩니다.

**Pilot**

- envoy 설정 및 관리
- service discovery 기능 제공
- Pilot은 service discovery mechanism을 추상화

**Mixer**

- Service Mesh 액세스 제어 및 정책 관리
- 각종 모니터링 지표 수집

**Citadel**

- 보안 모듈
- 서비스를 사용하기 위한 인증
- TLS 암호화, 인증서 관리

**Galley**

- Istio configuration의 유효성 검사

> Istio 구조

![img](https://istio.io/latest/docs/ops/deployment/architecture/arch.svg)    



<br>

**참고 자료**

[2018 Cloud Hackathon Tech Session - Kubernetes](https://www.youtube.com/watch?v=rdyUAduXi48&feature=emb_title)

[Istio](https://istio.io/latest/)

[Knative](https://knative.dev/docs/)

[조대협 블로그](https://bcho.tistory.com/1323?category=731548)