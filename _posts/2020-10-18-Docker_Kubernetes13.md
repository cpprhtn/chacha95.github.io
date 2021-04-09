---
layout: post
title: MLOps Zero to Hero 13 - Katibe
tags: [MLOps]
use_math: true
---

# Katibe란? 

Katib는 Kubeflow Components로서, Hyperparameter tunning, NAS(Neural Architecture Search)을 위한 kubernetes 위에서 동작하는 시스템입니다. 

Katib는 TensorFlow, PyTorch, Apache MXNet, XGBoost 등 다양한 머신러닝 프레임워크를 지원합니다.

<br>

## Katib 핵심 요소

Katib에는 **Experiment**, **Suggestion**, **Trial**, **Job**이 핵심입니다.

### Experiment

Experimen는 목표로 하는 대상 값을 찾기 위해서, Hyperparameters 값들을 찾는 탐색 작업을 합니다. 

Experiment에는 다음으로 구성되있습니다.

- **Objective:** Hyperparameters 튜닝 작업 통해서, 이루고자 하는 목표를 정의해야 합니다. Hyperparameters 조정 작업에서 metric을 최대화할지 최소화할지 여부도 지정합니다.
- **Search Space:** Hyperparameters 튜닝 작업시 사용해야 할 모든 Hyperparameters 값과 Hyperparameters의 제약 조건을 정의해야 합니다. 예를 든다면, Learning rate는 0.1부터 0.5까지의 값을 사용하고, optimizer는 sgd와 adam을 사용한다고 정의할 수 있습니다.
- **Search Algorithm:** Hyperparameters 튜닝 작업시 사용할 알고리즘을 정의해야 합니다. Random Search, Grid Search, Bayesian Optimization 등 다용한 알고리즘을 사용할 수 있습니다. 최적의 Hyperparameters 값을 검색 할 때 사용할 알고리즘입니다.

Katib를 사용해서 최적의 Hyperparameters를 찾으려면, **Experiment**라는 사용자 리소스를 생성하면 됩니다.

### Suggestion

Katib는 각 Experiment 사용자 리소스 마다 하나의 **Suggestion** 사용자 리소스를 생성합니다. Suggestion은 탐색 알고리즘이 제안한 Hyperparameters 값들의 세트를 가지고 있습니다. Katib 는 제안된 Hyperparameters 값들을 세트별로 평가하기 위한 Trial을 작성합니다.

### Trial

Trial은 제안된 Hyperparameters를 평가하기 위한 하나의 작업을 의미하는 사용자 리소스입니다. 제안된 매개 변수 값들을 Woker Job 인스턴스 할당해서 실행합니다. 

Experiment는 목표나 설정한 최대 시도 횟수에 도달 할 때까지 Trial을 계속 실행합니다.

### Worker Job

Worker Job은 Trial을 평가하고 목표 값을 계산하는 프로세스를 의미합니다. 제안된 Hyperparameters을 넘겨 받아, 실제 모델을 학습하게 됩니다.

다음은 사용 가능한 Worker Job의 유형입니다.

- **Kubernetes Job**
- **Kubeflow TFJob** (분산 처리 지원)
- **Kubeflow PyTorchJob** (분산 처리 지원)

<br>

## Search Algorithm

Katib에서 제공하는 탐색 알고리즘은 여러가지 있지만, 2가지만 소개해 드리면 다음과 같습니다.

### Grid Search

**Grid Search**는 Hyperparameters 공간에서 지정한 조건을 모두 조합해 전부 탐색하는 것을 말합니다. Grid Search는 모든 가능성에 대해 탐색을 수행하기 때문에, 탐색 프로세스를 매우 길게 만듭니다. 그래서 Grid Search는 만들어낼 수 있는 Hyperparameters의 탐색 조합이 적은 경우에만 유용하게 사용할 수 있습니다.

### Random Search

**Random Search**는 Grid Search의 대안으로, 조합하여 탐색해야할 Hyperparameters 수가 많을 때 사용하면 좋습니다. Random Search는 무작위로 Hyperparameters를 선택하여 조합을 만들어냅니다. Random Search는 이름 그대로 몇가지 경우에 대해 Random하게 조합을 생성하기에 탐색 시간이 짧습니다. 때문에 Random Search는 모든 가능성에 대한 탐색이 불가능할 때 사용하기 좋은 알고리즘입니다. 

> Grid search와 Random search

![](https://miro.medium.com/proxy/1*ZTlQm_WRcrNqL-nLnx6GJA.png)

<br>

**참조 강의**

[Kubeflow 101](https://www.youtube.com/playlist?list=PLIivdWyY5sqLS4lN75RPDEyBgTro_YX7x)

[Kubeflow doc](https://www.kubeflow.org/docs/)

