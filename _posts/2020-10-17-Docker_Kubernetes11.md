---
layout: post
title: MLOps 10 - kfp(kubeflow pipeline) 소개
tags: [MLOps]
use_math: true
---

# Kubeflow Pipelines?

Kubeflow Pipelines는 Docker 컨테이너를 기반으로하는 확장 가능한 ML workflow를 구축하고 deployment하기위한 플랫폼입니다. Argo CD 기반으로 만들어졌습니다.

Kubeflow Pipelines 플랫폼은 다음으로 구성됩니다. 

- Experiment, Job 및 Run을 관리하고 추적하기위한 UI

- Pipelines 및 components를 정의하고 조작하기위한 SDK

- SDK를 사용하여 시스템과 상호 작용하기위한 jupyter notebook

### Kubeflow Pipelines의 목표

- **End to end orchestration:** 기계 학습 pipeline의 오케스트레이션을 단순화합니다.

- **Experiment:** 다양한 아이디어와 기술을 쉽게 시도하고 다양한 trial / experiment를 관리 할 수 있습니다.

- **Reuse:** components와 pipelines을 재사용하여 end to end solution을 빠르게 만들 수 있습니다.

<br>

## KFP DAG

Kfp(kubeflow pipeline)은 kubeflow echo system의 한 부분으로, 대규모 ML 모델을 train 및 test하고 이를 효과적으로 관리할 수 있게 도와줍니다. Kfp는 Kubeflow의 가장 중요한 기능 중 하나이며, ML Experiment를 reproducible, reusable하게 만듭니다.

Pipeline의 각 node는 container이며, **DAG**(Directed Acyclic Graph)로 표현됩니다.

> kfp example

![](https://user-images.githubusercontent.com/31475037/112990077-98d7b380-91a0-11eb-9d51-e9a17ef31afc.png)

Pipeline을 실행할 때 각 컨테이너는 Kubernetes 스케줄링에 따라 dependency를 고려하여 cluster 내에서 실행됩니다.

> Run pipeline in cluster

![](https://user-images.githubusercontent.com/31475037/112990070-970df000-91a0-11eb-853f-2747d0681532.png)

<br>

## Architecture

**Python SDK:** Kubeflow Pipelines 도메인 별 언어 (DSL)를 사용하여 component를 생성하거나 Pipelines을 지정

**DSL 컴파일러:** DSL 컴파일러는 Pipelines의 Python 코드를 정적 구성(YAML)으로 변환

**Pipelines 서비스:** Pipelines 서비스를 호출하여 정적 구성에서 실행되는 Pipelines을 만듬

**Kubernetes 리소스:** Pipelines 서비스는 Kubernetes API 서버를 호출하여 Pipelines을 실행하는 데 필요한 Kubernetes 리소스 (CRD)를 만듬

**오케스트레이션 컨트롤러:** 일련의 오케스트레이션 컨트롤러는 Pipelines을 완료하는데 필요한 컨테이너를 실행하며, 컨테이너는 가상 머신의 Kubernetes pod 내에서 실행됨

**Metadata:** Experimentation, Job, Pipelines 실행 및 모델 Evaluation.

**Artifact:**  Kubeflow Pipelines는 Minio 서버 또는 Cloud Storage와 같은 Artifact 저장소에 Artifact를 저장

**Agent 및 ML Metadata:** Pipelines Agent는 Pipelines 서비스에서 생성 된 Kubernetes 리소스를 감시하고 ML Meatadata 서비스에서 이러한 리소스의 상태를 유지

**Pipelines 웹 서버:** Pipelines 웹 서버는 다양한 서비스에서 데이터를 수집하여 현재 실행중인 Pipelines 목록, Pipelines 실행 기록, data artifact 목록, 개별 Pipelines 실행에 대한 디버깅 정보, 개별 Pipelines 실행에 대한 실행 상태를 표시합니다.

> Pipelines Architecture

![](https://user-images.githubusercontent.com/31475037/96213846-e4acec80-0fb4-11eb-8676-9f60603bf277.png)

<br>

# KFP Concepts

Kfp의 주요 concept들에 대해 소개합니다.

<br>

## Component

Components는 특정 프로그램이나, 특정 entrypoint를 컨테이너 안에서 표현하는 요소를 말합니다. 각 components는 독립되어있으며, components는 같은 프로세스안에서 같이 실행되서는 안됩니다. (메모리 공유도 x)

함수 개념과 굉장히 유사합니다. (input이 있고 output이 있음)

일반적으로 components는 다음과 같은 정보와 역할을 담당하며 이를 YAML format으로 describe 합니다.

- **Metadata**: name, description, etc
- **Interface**: input/output 정의(name, type, description, default value, etc)
- **data passing**: Program은 input, output data 정보를를 args로 받아드리며,  data 크기가 작은 경우 command-line args로 전달, data 크기가 큰 경우 파일로 저장한 뒤 파일의 경로를 전달(여기서 파일 경로를 하드 코딩해서는 안됨 e.g. /mnt/data/)

<br>

## Step

Step은 pipline안에 있는 component를 실행하는 것을 의미합니다.

<br>

## Experiment

Experiment는 다양한 pipeline configuration를 시도하는 workspace입니다. Experiment 안에는 수많은 Run들이 존재합니다.

<br>

## Run and Recurring run

Run은 Pipeline의 single exectuion입니다. Run 각 step에 대한 runtime graph, output artifact, log를 Kubeflow Pipelines UI에서 확인 가능합니다. 만약 Create Run시 Experiment 설정을 안해줬다면, Default Experiment에 Run이 생성됩니다.

**Recurring run**은 pipline 내 반복되는 run 입니다. Pipeline config, parameters, run trigger를 가집니다. concurrent run, parallel run 수를 설정합니다.

<br>

## Artifact

모델 train/test 과정 중에서 생성된 output들을 의미합니다.(e.g. log, model ...)

<br>

## DSL(Domain-Sepcific Language)

DSL는 특정 분야에 최적화된 프로그래밍 언어입니다. 비 전문가도 쉽게 사용토록 설계되었습니다. 대표적인 케이스가 DSL compiler를 이용해 python 코드를 yaml spec으로 바꾸는 경우가 있습니다.

<br>

# KFP SDK

Kfp SDK는 아래 package들로 구성되어 있습니다.

### `kfp.compiler`

`kfp.compiler.Compier.compile`은 Python DSL code를 single static config(YAML format)으로 바꿔줍니다. DSL compilers는 python 코드를 yaml 파일로 변환

### `kfp.components`

pipeline components와 interacting하는 패키지

- `kfp.components.func_to_container_op`는 python function을 pipeline component로 바꿈
- `kfp.components.load_component_from_file`은 yaml로 describe된 pipeline component를 파일로 로드
- `kfp.components.load_component_from_url`는 yaml로 describe된 pipeline component URL로 부터 로드

### `kfp.dsl`

`kpf.dsl`는 dsl에 대해 관리하는 패키지 입니다.

- `kfp.dsl.PipelineParam`은 pipeline parameters에 대해 정의
- `kfp.dsl.pipeline`는 python으로 정의된 pipeline을 yalm 파일로 바꿈
- `kfp.dsl.VolumeOP`는 현 클러스터 내에서 PVC를 생성

### `kfp.Client`

Kfp web-server와 통신하는 client입니다. Compile된 pipeline을 수동으로 kfp web-ui에 올리지 않고, Client 패키지를 통해 이용 가능합니다. 보통 kubeflow 설치시 같이 있는 jupyter server에서 실행시킵니다.

- `kfp.Client.create_experiment`는 pipeline experment를 생성하고 experiment object를 반환
- `kfp.Client.run_pipeline`는 pipeline을 실행하고, run object 반환
- `kfp.Client.create_run_from_pipeline_func`는 pipeline을 컴파일하고, Kubeflow Pipeline에 submit하고 실행시킴
- `kfp.Client.upload_pipeline_version`는 pipeline을 Kubeflow Pipelines에 upload(version과 같이)

<br>

# Building Pipelines and Components

Kfp SDK에서는 총 4가지 building 방식이 존재하지만, 여기선 3가지 방식만 소개하겠습니다. 

<br>

## Creating components within your application code

이 방식은 application 외부 프로그램의 도움 없이 pipeline을 구축하는 방식입니다.

### 1. Write Python code

```python
def my_func(a:str, b:str) -> str:
    ...
```

### 2. function to component

`kfp.dsl.python_component`를 사용해 python function을 component로 변환합니다.

```python
@kfp.dsl.python_component(
  name='example',
  description='example',
)
def my_func(a:str, b:str) -> str:
    ...
```

### 3. create container image

`kfp.compiler.build_python_component`를 사용해 component를 담은 image(container) 생성합니다.

```python
my_op = kfp.compiler.build_python_component(
  component_func=my_python_func,
  staging_gcs_path=OUTPUT_DIR,
  target_image=TARGET_IMAGE)
```

### 4. Write pipeline

`kfp.dsl.pipeline`을 이용해 pipeline을 생성합니다.

```python
@kfp.dsl.pipeline(
  name='pipeline',
  description='pipeline',
)
def my_pipeline(param_1: PipelineParam, param_2: PipelineParam):
  my_step = my_op(a='a', b='b')
```

### 5. Compile the pipeline

`kfp.compiler.Compiler.compile`을 이용해 pipeline 코드를 python에서 YAML format으로 변경합니다.

```python
kfp.compiler.Compiler().compile(my_pipeline, 'pipeline.zip')
```

### 6. run pipeline

clinet를 이용해 pipeline을 업로드

```python
client = kfp.Client()
exp = client.create_experiment(name='example')
run = client.run_pipeline(exp.id, 'pipeline', 'pipeline.zip')
```

<center><img src="https://www.kubeflow.org/docs/images/pipelines-sdk-within-app.svg"></center>



<br>

## Creating components from existing application code

기존 컨테이너화된 app을 components로 만들어 외부에서 component와 pipeline을 build하는 방식입니다. 이 방식을 이용해 reproducible(재사용성) 증가 시키고 보다 편리하게 관리합니다.

### 1. Build & upload docker image

docker image를 빌드한 후 repository에 upload 합니다.

### 2. create components pull docker image

docker image를 pull 받아 `kfp.dsl`를 통해 components로 바꿉니다.

```python
@kfp.dsl.component
def my_component(my_param):
  ...
  return kfp.dsl.ContainerOp(
    name='My component name',
    image='gcr.io/path/to/container/image'
  )
```

### 3. Define a pipeline and include all components

pipeline 함수를 정의합니다.

```python
@kfp.dsl.pipeline(
  name='My pipeline',
  description='My machine learning pipeline'
)
def my_pipeline(param_1: PipelineParam, param_2: PipelineParam):
  my_step = my_component(my_param='a')
```

### 4. compile pipeline

정의된 pipeline을 compile해 yaml 파일로 변경하고 압축합니다.

```python
kfp.compiler.Compiler().compile(my_pipeline, 'pipeline.zip')
```

### 5. run pipeline

`Client()` 클래스를 이용해 kfp server에 experiment를 정의하고 run을 합니다.

```python
client = kfp.Client()
exp = client.create_experiment(name='example')
run = client.run_pipeline(exp.id, 'pipeline','pipeline.zip')
```

<center><img src="https://www.kubeflow.org/docs/images/pipelines-sdk-outside-app.svg"></center>

<br>

## Using prebuilt, reusable components in your pipeline

Reusable, prebuilt한 components는 누군가가 yaml 파일로 describe된 파일을 pipeline 상에서 load 하는 방식입니다. 이 방식이 현재 kfp에서 밀고 있는 방식이며, `kfp.dsl.ContainerOp` 사용시 이 방식을 이용해 pipeline과 components를 구성해달라고 warning 문구가 뜹니다. 왜냐하면 이 방식 외에 3가지 방식이 kubernetes resource들과 잘 어울리지 못해 사용이 불편하다는 유저들의 issue가 굉장히 많아 아마 이러한 방식을 선택한게 아닐까 추측됩니다.

### 1. components yaml 파일로 describe

>  yaml로 describe된 Components 예시

```yaml
name: train mnist model
description: train mnist model
inputs:
  - {name: storage, type: string}
outputs:
  - {name: logdir, type: string}
implementation:
  container:
    image: train_image_location
    command: ['python', '/mnist.py']
    args: ['--storage', {inputValue: storage}]
    fileOutputs: 
      logdir: /logdir.txt
```

### 2. components를 load

yaml로 describe된 components를 `kfp.components.load_component_from_file(filename)` or `kfp.components.load_component_from_url()`함수를 이용해  load 합니다.

```python
my_op = kfp.components.load_component_from_file('./train/component.yaml')
```

### 3. components 사용해 pipeline을 구축 

load된 components를 이용해 pipeline을 구축합니다.

```python
@kfp.dsl.pipeline(
  name='My pipeline',
  description='My machine learning pipeline'
)
def my_pipeline():
  my_step = my_op()
```

### 4. compile pipeline

```python
kfp.compiler.Compiler().compile(my_pipeline, 'pipeline.zip')
```

### 5. run pipeline

```python
client = kfp.Client()
exp = client.create_experiment(name='example')
run = client.run_pipeline(exp.id, 'pipeline','pipeline.zip')
```



<center><img src="https://www.kubeflow.org/docs/images/pipelines-sdk-reusable.svg"></center>

<br>

**참조 강의**

[Kubeflow 101](https://www.youtube.com/playlist?list=PLIivdWyY5sqLS4lN75RPDEyBgTro_YX7x)

[Kubeflow doc](https://www.kubeflow.org/docs/)

[Introduction to the Pipelines SDK](https://www.kubeflow.org/docs/components/pipelines/sdk/sdk-overview/)

[도메인 특화 언어(DSL)에 관한 설명](https://www.jetbrains.com/ko-kr/mps/concepts/domain-specific-languages/)

[Concepts](https://www.kubeflow.org/docs/components/pipelines/overview/concepts/)

[Data science workflows on Kubernetes with Kubeflow Pipelines (Part 1)](https://www.manceps.com/articles/tutorial/data-science-workflows-on-kubernetes-with-kubeflow-pipelines-part-1)

