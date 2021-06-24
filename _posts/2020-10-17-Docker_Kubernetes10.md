---
layout: post
title: MLOps 9 - kubeflow components
tags: [MLOps]
use_math: true
---

이번 포스트에선 kubeflow의 **components(구성 요소)**들에 대해 다루겠습니다.

# Dashboard

Kubeflow deployment에는 클러스터에 deployment 된 Kubeflow components에 대한 빠른 액세스를 제공하는 중앙 dashboard가 포함됩니다. dashboard에는 다음 기능이 포함됩니다.

- 특정 작업에 대한 바로 가기, 최근 pipelines 및 jupyter notebook 목록 등의 정보를 간편히 제공
- Pipeline, Katib, Jupyter notebook 등을 포함하여 클러스터에서 실행되는 components의 UI 제공

### Kubeflow UI 개요

Kubeflow UI에는 다음이 포함됩니다.

- **Home:** Kubeflow components 간 탐색을위한 중앙 dashboard
- **Pipeline:** Kubeflow Pipelines
- **Notebook Servers**
- **Katib:** Hyperparameter tuner
- **Artifact Store:** Artifact metadata 추적
- **Manage Contributors:** Kubeflow deployment의 namespace에서 사용자 접근 관리

> Dashboard

![](https://user-images.githubusercontent.com/31475037/96206784-a0195500-0fa4-11eb-94b1-edf8778def81.png)

<br>

# Metadata

Kubeflow에서 ML workflow가 생성하는 metadata를 추적, 관리하여 유저가 ML workflow를 관리하도록 합니다. 여기서 metadata는 모델, dataset 및 기타 artifact에 대한 정보를 의미합니다. 여기서 artifact는 ML workflow에서 components의 입출력을 형성하는 파일입니다.

<br>

# Fairing

Kubeflow Fairing은 하이브리드 클라우드 환경에서의 시스템을 구축하여, train and deployment하는 프로세스를 간소화합니다.

Kubeflow Fairing을 사용하고 몇 줄의 코드를 추가하면 Python 코드 또는 jupyter notebook에서 직접 로컬 혹은 클라우드에서 ML train job을 실행할 수 있습니다. Train job이 완료된 후 Kubeflow Fairing을 사용하여 학습된 모델을 end point로 deployment 할 수 있습니다.

Fariring 사용시 다음의 이점이 있습니다. 

- **손쉬운 ML train job 패키징:** ML engineer가 ML 모델 학습 코드와 해당 코드의 환경을 Docker 이미지로 쉽게 패키징
- **하이브리드 클라우드 환경에서 손쉽게 ML 모델 학습:** 인프라를 추상화 시켜, ML 모델 train을 위한 API를 제공
- **학습 모델에 대한 serving과 통합 deployment 프로세스 간소화:** On-premise 서버에서 학습시킨 모델을 클라우드 환경에 쉽게 모델을 serving하고 애플리케이션과 통합하여 쉽게 deployment 할 수 있습니다.

> On-premise server와 cloud를 혼용해서 사용가능케 함

![](https://user-images.githubusercontent.com/31475037/95158516-bec66180-07d6-11eb-9016-46e7b77d7ec4.png) 

<br>

# Kubeflow Pipelines

Kubeflow Pipelines는 Docker 컨테이너를 기반으로하는 확장 가능한 ML workflow를 구축하고 deployment하기위한 플랫폼입니다.

Kubeflow Pipelines 플랫폼은 다음으로 구성됩니다. 

- Experiment, Job 및 Run을 관리하고 추적하기위한 UI

- Pipelines 및 components를 정의하고 조작하기위한 SDK

- SDK를 사용하여 시스템과 상호 작용하기위한 jupyter notebook

### Kubeflow Pipelines의 목표

- **End to end orchestration:** 기계 학습 pipeline의 오케스트레이션을 단순화합니다.

- **Experiment:** 다양한 아이디어와 기술을 쉽게 시도하고 다양한 trial / experiment를 관리 할 수 있습니다.

- **Reuse:** components와 pipelines을 재사용하여 end to end solution을 빠르게 만들 수 있습니다.

<br>

# Multi-user Isolation

프로덕션 환경에서는 서로 다른 팀과 사용자간에 동일한 리소스 풀을 공유해야하는 경우가 많습니다. 사용자는 실수로 서로의 리소스를 보거나 변경하지 않고 자신의 리소스를 격리하고 보호 할 수있는 안정적인 방법이 필요합니다.

Kubeflow deployment에서 namespace 및 사용자 생성 리소스에 대한 액세스 제어를 하는 multi-user isolation을 지원합니다. 이러한 격리를 통해 다른 유저가 다른 사람의 리소스를 제거하거나 하는 문제를 해결합니다.

**Manger:** Manager는 Kubeflow 클러스터를 만들고 유지 관리하는 사람입니다. 이 사람은 다른 사람에게 액세스 권한을 부여 할 권한이 있습니다.

**User:** User는 클러스터의 일부 리소스 집합에 액세스 할 수있는 사람입니다. User는 관리자로부터 액세스 권한을 부여 받아야합니다.

**Profile:** Profile은 사용자가 소유 한 모든 Kubernetes 클러스터의 그룹입니다.

<br>

# KFServing

KFServing은 Kubernetes에서 serverless inference를 지원하고 TensorFlow, XGBoost, scikit-learn, PyTorch, ONNX와 같은 ML 프레임 워크에 대해 고성능의 추상화 인터페이스를 제공 합니다.

### 주요 기능

임의의 프레임 워크에서 ML 모델을 제공하기위한 Kubernetes 커스텀 리소스 정의를 제공합니다.

Auto scailing, networking, status check, 서버 구성의 복잡성을 캡슐화하여 GPU auto scailing, 카나리아 롤아웃과 같은 기능을 제공합니다.

> KFServing

![](https://user-images.githubusercontent.com/31475037/96206778-9e4f9180-0fa4-11eb-883f-8cfeb02d2969.png)

<br>

# Katib

Katib는 Hyperparameter tunning and NAS(Neural Architecture Search)를 하는 tool입니다. 현재로선 Katib를 사용해 보다 편하게 Hyperparameter를 찾게 도와줍니다.

<br>

*Artifacts: 학습 프로세스 혹은 전체 pipeline에서 생성되는 outputs을 일컫습니다. 예를 들어 model artifacts는 학습된 model weights를 의미합니다.*

<br>

**참조 강의**

[Kubeflow 101](https://www.youtube.com/playlist?list=PLIivdWyY5sqLS4lN75RPDEyBgTro_YX7x)

[Kubeflow doc](https://www.kubeflow.org/docs/)

