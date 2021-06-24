---
layout: post
title: What is machine learning?
tags: [ML]
---

# Machine learning의 개념

Machine learning(기계 학습)은 데이터로부터 학습하도록 컴퓨터를 프로그래밍하는 방법입니다.

> AI, Machine learning, Deep learning



<center><img src="https://www.sumologic.com/wp-content/uploads/compare_AI_ML_DL.png" width="90%"></center>



예를 들어스팸 필터는 스팸 메일과 일반 메일의 샘플을 이용해, 스팸 메일을 구분하는 법을 배우고 이를 구분합니다.

<br>

## 왜 machine learning을 사용하는가?

전통적인 접근 방법으로 문제를 풀려하면, 문제가 단순치 않아 문제를 정의하기 쉽지 않고, 규칙이 점차 길고 복잡해져 유지 보수하기 힘들어 집니다.

> 전통적 접근 방식

<center><img src="https://user-images.githubusercontent.com/31475037/59028100-a9b18980-8895-11e9-8dbd-34105da26ad8.png"></center>

그에 비해 machine learning의 경우 데이터셋 구축 및 모델 학습 pipeline을 잘 구축한다면, 모델이 특정 패턴을 찾아 문제를 해결합니다. 또한 모델 학습 pipeline의 경우 첫 구축이 어렵지, 이후 시스템 유지보수가 상대적으로 쉽습니다.

> machine learning을 이용한 시스템

<center><img src="https://user-images.githubusercontent.com/31475037/59028106-ab7b4d00-8895-11e9-9a5d-9c85406c9302.png"></center>

<br>

# machine learning 시스템의 종류

## Supervised Learning(지도 학습)

Supervised learning은 훈련 데이터에 레이블(답)이 달려 있는 데이터를 이용해 학습하는 방법입니다. 

대표적인 supervised learning 방법은 다음과 같습니다

* Linear Regression(회귀)
* Logistic Regression(분류)
* Support Vector Machine
* Random Forests
* Neural Network



여기서 지도학습은 크게 Classification(분류)와 Regression(회귀)로 나뉘어집니다. 

### Classification

먼저, **Classification**은 주어진 데이터를 정해진 카테고리에 따라 분류하는 문제를 말합니다.

예를 들어, 이메일이 스팸메일인지 아닌지를 예측한다고 하면 이메일은 **스팸메일**, **정상 메일**로 라벨링 될 수 있을 것입니다. 비슷한 예시로 암을 예측한다고 가정했을 때 이 종양이 **악성 종양인지** , **아닌지로** 구분할 수 있습니다. 이처럼 **맞다**, **아니다**로 구분되는 문제를 **Binary Classification** 이라고 부릅니다.

그러나 분류 문제가 모두 **맞다**, **아니다**로 구분되진 않습니다. 예를 들어, 공부시간에 따른 전공 **Pass**, **Fail**을 예측한다 가정해 봅시다. 이는 Binary Classifiaction 으로 볼 수 있습니다. 반면, 전공 시험 성적에 따른 학점을 **A, B, C, D, F** 네가지 class로 예측해야하는 경우도 있습니다. 이러한 분류를 **Multi-class Classification**이라고 합니다.

### Regression

**Regression**은 연속된 값을 예측하는 문제를 말합니다. 주로 어떤 패턴이나 트렌드, 경향을 예측할 때 사용됩니다.  

<br>

## Unsupervised Learning(비지도 학습)

훈련 데이터에 레이블이 없습니다. 시스템이 레이블된 데이터 없이 학습을 해야 합니다.

대표적인 unsupervised learning 방식은 다음과 같습니다

* Clustering

<br>

## Reinforcemence Learning(강화 학습)

가장 큰 보상을 얻기 위해 최상의 전략을 스스로 학습하는 방식입니다. 보통 simulation 환경에서 학습을 합니다. 

대표적 방식은 다음과 같습니다

* DQN

<br>

# Machine learning의 주요 도전 과제

### 충분하지 않은 데이터

Machin learning  모델이 잘 학습되려면, 데이터가 굉장히 많아야 합니다.

아주 간단한 문제일지라도 수천개의 데이터가 필요합니다.

### 대표성 없는 학습 데이터

모델이 성공적으로 예측하려면,  대표성 있는 학습 데이터가 필요합니다.

### 낮은 품질의 데이터

학습 데이터가 error로 가득하다면 machine learning 모델이 잘 동작하지 않습니다.

### Overfitting(과적합)

Overfitting은 모델이 학습 데이터의 정답은 잘 맞추지만, Generalization(일반성)이 떨어지는 경우를 말합니다. 

Overfitting을 막기위해 Regularization(정규화) 기법을 많이 씁니다.

<br>



