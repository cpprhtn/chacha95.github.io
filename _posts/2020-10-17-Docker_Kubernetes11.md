---
layout: post
title: MLOps Zero to Hero 11 - kubeflow pipelines 소개
tags: [MLOps]
use_math: true
---

# Kubeflow Pipelines?

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

## Pipelines?

Pipelines는 workflow의 모든 component와 이러한 component가 그래프 형태로 표현되는 방식입니다. Pipelines에는 Pipelines을 실행하는데 필요한 입력의 정의와 각 component의 입출력이 포함됩니다.

### Pipelines의 런타임 실행 그래프

> Kubeflow Pipelines UI에있는 pipelines의 런타임 실행 그래프

![](https://user-images.githubusercontent.com/31475037/96213849-e5de1980-0fb4-11eb-8871-1a5da0779862.png)

<br>

## Architecture overview

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

**참조 강의**

[Kubeflow 101](https://www.youtube.com/playlist?list=PLIivdWyY5sqLS4lN75RPDEyBgTro_YX7x)

[Kubeflow doc](https://www.kubeflow.org/docs/)

