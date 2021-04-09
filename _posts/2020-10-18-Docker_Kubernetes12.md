---
layout: post
title: MLOps Zero to Hero 12 - kubeflow model Serving
tags: [MLOps]
use_math: true
---

Model Serving이란 다른 애플리케이션에서 ML 모델을 사용할 수 있도록 모델을 배포하는 것입니다.

다양한 Serving tool들이 있지만, **TensorFlow Serving**, **TorchServe**, **KFServing**을 리뷰하겠습니다.

<br>

# TensorFlow Serving(TF Serving)

TensorFlow Serving은 프로덕션 환경을 위해 설계된 ML을 위한 유연한 고성능 production system입니다. TensorFlow Serving을 사용하면 동일한 서버 아키텍처와 API를 유지하면서 새로운 알고리즘과 실험을 쉽게 배포 할 수 있습니다.

<br>

## Key Concepts

### **Servables**

Servable은 클라이언트가 연산을 수행하는 데 사용하는 기본 개체입니다. 또한 Servable은 TensorFlow Serving의 핵심적 추상화 개체입니다.  

### **Servable Version**

TensorFlow Serving은 하나의 서버 인스턴스가 하나 이상의 서빙 가능 버전을 처리 할 수 있습니다. 이를 통해 새로운 알고리즘 구성, 가중치 및 기타 데이터를 시간에 따라 로드 할 수 있습니다. 버전을 사용하면 둘 이상의 서빙 가능 버전을 동시에로드 할 수 있으므로 점진적인 롤아웃 및 experimentation을 지원합니다. 제공 시간에 클라이언트는 특정 모델에 대해 최신 버전 또는 특정 버전 ID를 요청할 수 있습니다.

### Model

TensorFlow Serving은 모델을 하나 이상의 서빙 가능 항목으로 나타냅니다.

### Loader

Loader는 서비스의 lifecycle을 관리합니다. Loader API는 관련된 특정 학습 알고리즘, 데이터 또는 제품 사용 사례와 독립적인 인프라를 지원합니다.

### **Sources**

Sources는 서비스를 찾아 제공하는 플러그인 모듈입니다. TensorFlow Serving의 Sources 인터페이스는 임의의 저장소 시스템에서 제공 가능한 항목을 검색 할 수 있습니다.

### **Aspired Versions**

Aspired versions은 로드되고 준비되어야하는 제공 가능한 버전들을 나타냅니다. 소스는 한 번에 제공 가능한 단일 스트림에 대해 제공 가능한 버전들을 전달합니다.

### **Managers**

Manager는 Servable의 전체 lifecycle를 관리 합니다.

- Servable 로드
- 서비스 제공
- Servable 언로드

Managers는 source를 받아 버전을 추적합니다. Managers가 source의 요청을 실행하려 할 때,  필요한 리소스를 사용할 수없는 경우 원하는 버전의 로드를 거부 할 수 있습니다. 

요약하자면

1. **Sources**가 **Servable versions**를 위해 **Loaders**를 생성합니다.
2. **Loaders**가 **Aspired Versions**을 **Manager**에게 보내고, 이를 통해 **Client**의 요청에 **Loads**, **Serves**에 답변 합니다.

> Lifecycle of a Servable

![](https://user-images.githubusercontent.com/31475037/96213587-58022e80-0fb4-11eb-8cca-a87499d5f009.png)

<br>

# TorchServe

PyTorch의 편의성과 효율성 덕분에 연구 분야에선 많이 채택되었지만, 현실에서 PyTorch 모델을 프로덕션에 적용 할 수 있는지에 대해는 의문이 있을 수 있습니다. 그러나 Facebook에선 PyTorch를 사용하여 하루에 **수백조**번 inference를 실행하였고, 이 노하우를 기반으로 TorchServe를 출시하였습니다.

<br>

## TorchServe Architecture

**Front-end** : TorchServe의 요청 / 응답을 처리하는 component입니다. 서빙 component 부분은 클라이언트로부터 오는 요청 / 응답을 모두 처리하고 모델의 lifecycle를 관리합니다.

**Worker process:** 모델에 대한 inference를 실행합니다.

**Model:** 모델은 같은 다른 모델과 함께 클라우드 스토리지 또는 로컬 호스트에서 로드 할 수 있습니다.

**Plugin:** TorchServe에 연동 할 수있는 사용자 지정 엔드 포인트 혹은 인증 기능들을 추가 할 수 있습니다.

**Model Storage:** 로드 가능한 모든 모델들이 존재하는 디렉토리입니다.

> TorchServe Architecture

![](https://user-images.githubusercontent.com/31475037/96213585-56386b00-0fb4-11eb-8a40-7e6ffddca6b1.png)

<br>

# KF Serving

**Beta version**

KFServing은 Kubernetes에서 serverless inference를 지원하고 TensorFlow, XGBoost, scikit-learn, PyTorch, ONNX와 같은 일반적인 머신 러닝 (ML) 프레임 워크에 대해 고성능의 높은 추상화 인터페이스를 제공, 프로덕션 모델을 해결합니다. 다만 아직 베타 버전이라 아직 불안정 합니다.

### 주요 기능

임의의 프레임 워크에서 ML 모델을 제공하기위한 Kubernetes 커스텀 리소스 정의를 제공합니다.

Auto scailing, networking, status check, 서버 구성의 복잡성을 캡슐화하여 GPU 자동 확장, 카나리아 롤아웃과 같은 최첨단 서비스 기능을 ML 배포에 제공합니다. 

> KFServing

![](https://user-images.githubusercontent.com/31475037/96206778-9e4f9180-0fa4-11eb-883f-8cfeb02d2969.png)

<br>

**참고 자료**

[PyTorch](https://pytorch.org/blog/model-serving-in-pyorch/)

[Architecture | TFX | TensorFlow](https://www.tensorflow.org/tfx/serving/architecture)