---
layout: post
title: MLOps 8 - kubeflow 소개
tags: [MLOps]
use_math: true
---

# 배경지식

현실에서 ML 모델을 만들어 서비스에 적용시키는 일은, 모델을 만드는 시간보다 데이터 수집과 분석 그리고 모델 튜닝에 더 많은 시간이 소모되며, 점점 더 시스템이 복잡해 졌습니다. 그래서 이런 과정을 묶어 파이프라인으로 구축하게 됩니다. 그런데 이러한 파이프라인이 늘어나고, 시스템이 더더욱 복잡해지자 유지보수가 힘들어지기 시작하자 kubeflow와 같은 '**ML 플랫폼**'이 등장하였습니다. 

> production level의 ML project에서 순수 ML 모델 개발은 5%도 채 안됨

![](https://user-images.githubusercontent.com/31475037/94651765-f1cea800-0333-11eb-8d34-761ee240d7d8.png)



<br>

# Kubeflow의 등장

Kubeflow는 kubernetes 위에서 돌아가는 오픈 소스들을 가져다 붙여 쓸수 있는 확장형 **ML 플랫폼** 입니다. Kubeflow는 간결한 CLI interface, 직관적인 대시 보드와 여러 클라우드와 On-premise를 섞어 사용 가능한 구조를 지녔기에 굉장히 핫합니다. 또한 **Data scientiest**, **ML engineer**, **ML researcher** 모두에게 필요한 요소들을 제공합니다.

### Data scientiest

Data 전처리를 위한 ML pipeline을 구축하고, 실험하려는 data scientiest를 위한 기능을 제공합니다.

### ML engineer

개발, 테스트 및 production level 서비스를 위해 다양한 환경에 ML 시스템을 배포하도록 기능을 지원합니다.

### ML researcher

Jupyter notebook을 기본으로 하여 tesnorflow, pytorch, MXNet 등을 이용하여 모델 개발이 가능합니다.

> kubeflow Architecture

![](https://www.kubeflow.org/docs/images/kubeflow-overview-platform-diagram.svg)

<br>

## ML Workflow

ML workflow는 ML 실험과 개발을 보다 간편하게 하기 위해 제공되는 툴입니다. 크게 두가지 단계로 나뉘어져 있습니다.

### Experimental phase

실험 단계에서는 초기 가정을 기반으로 실험을 통해 모델을 개발합니다. 

보통 다음 업무를 진행합니다.

- ML 시스템으로 해결하려는 문제를 식별
- ML 모델 학습에 필요한 데이터를 수집하고 분석 
- ML 프레임 워크와 모델을 선택하고 모델 초기 버전 제작

### Production phase

프로덕션 단계에서는 실험으로 만들어진 모델을 production level에서 사용할 수 있도록 개발합니다.

보통 다음 업무를 진행합니다.

- 학습에 알맞게 데이터 변환
- ML 모델의 성능을 모니터링
- ML 모델 fintune

> 예시

![](https://www.kubeflow.org/docs/images/kubeflow-gcp-e2e-tutorial-simplified.svg)

<br>

## Kubeflow 특징

Kubeflow는 새로운 툴들을 만들기보단 kubernetes 오픈소스 생태계에 있는 툴을 가져와 쓸수 있게 만들었습니다. 

Cloud나 On-Premise 환경에 상관 없이 동일한 환경에서 개발할 수 있기 때문에 특정 벤더나 플랫폼에 종속되지 않습니다. 예를들어 모델 학습은 On-premise에서 학습을하고, 학습된 모델은 cloud에 올려 서비스하는 일도 가능합니다.

> Cloud only

![](https://user-images.githubusercontent.com/31475037/95158521-bf5ef800-07d6-11eb-8a15-0b95aec3dda5.png)



> On-premise와 cloud를 혼용해서 사용

![](https://user-images.githubusercontent.com/31475037/95158516-bec66180-07d6-11eb-9016-46e7b77d7ec4.png) 

<br>

Kubeflow의 특징을 요약하자면 다음과 같습니다. 

- 다양한 인프라에 쉽고 반복 가능한 배포 (예 : local pc에서 실험한 다음 클라우드에 배포)
- 요구에 따라 가능한 스케일링(auto scailing)
- 전체 ML 개발 과정 통합 관리, On-premise/cloud 제한 없이 배포 지원 
- ML 워크플로우를 제공하며, 여기서 ML 워크플로우라는 오픈소스는 ML project의 라이프사이클을 관리하는 오픈소스이며, kubeflow는 kubernetes 환경에서 ML 워크플로우를 관리, 배포, 확장하게 도와주는 ML 플랫폼임
- kubernetes 위에서 돌기에 kubernetes 생태계에 있는 오픈소스를 가져와 사용 가능(Argo, Prometheus...)
- kubernetes가 업계 de facto(표준)가 된 지금, 강력한 확장성을 지님
- kubeflow는 웹으로 접속하는 GUI를 제공해 편리한 UI/UX를 제공함

<br>

# Kubeflow: The Answer to ML in Kubernetes?

ML(Machine Learning) 라이프 사이클의 각 단계를 지원하는 툴들과 합쳐진 Kubeflow는 ML 표준화 분야에선 타의 추종을 불허합니다. 그렇다면 Kubeflow는 몇 달 만에 Kubernetes/production level ML 환경을 근본적으로 바꿀까요? 이상적으론 가능하지만 그렇지 않을 수도 있습니다.

> Kubeflow 내부는 여러 툴들을 연동해 추상화를 시켰음

![](https://user-images.githubusercontent.com/31475037/96206125-364c7b80-0fa3-11eb-90de-5ec120184341.png)

<br>

## ML과 Kubernetes의 만남

Kubernetes와 ML은 각기 러닝 커브가 굉장히 높습니다. 두 가지 모두 포괄적으로 이해하는 engineer는 거의 없습니다. Kubeflow가 Kubernetes 수준의 편리성을 제공하려면, 서로의 분야에 대해 이해가 필요합니다. 대다수의 Kubernetes engineer는 vanilla Kubernetes에 Kubeflow를 설치했을 뿐입니다. 반면 대다수의 ML engineer는 kubernetes에 대한 이해도가 떨어져 제대로 사용하지도 못합니다.

> ML + Kubernetes = kubeflow가 최종적으로 원하는 Architecture

<center><img src="https://user-images.githubusercontent.com/31475037/96206122-351b4e80-0fa3-11eb-84af-cbd1cf825800.png"></center>

<br>

## ML 및 kubernetes 다음 단계

Kubeflow는 infra를 추상화하고 ML pipelines의 사용 편의성에 중점을 둡니다.하지만 Kubeflow는 아직 미성숙 단계이며, 대규모 팀에서 사용하기 위해선 세부적 설정이 필요합니다. 최종적으론 ML engineer, developer, data scientiest, operator 모두를위한 '**모든 사람을위한 클라우드 네이티브 ML**'이 탄생 할 수 있다는 것은 분명합니다.

<br>

**참조 강의**

[Kubeflow 101](https://www.youtube.com/playlist?list=PLIivdWyY5sqLS4lN75RPDEyBgTro_YX7x)

[Kubeflow doc](https://www.kubeflow.org/docs/)

[Kubeflow: The Answer to AI and ML in Kubernetes?](https://rancher.com/blog/2020/kubeflow-answer-to-ai-ml/)