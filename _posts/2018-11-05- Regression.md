---
layout: post
title: Regression
tags: [MLs]
---

# Regression 이란?

Regression이란 주어진 training 데이터로부터 가설을 세워 변수 사이의 모델(모형)을 구하는 예측을 말합니다.

대표적인 예로는 주택값 예측 모델이 있습니다.

> Regression의 간단한 예시 모델

Regression의 가장 간단한 예시로는 Linear Regression(선형 회귀)이 있습니다.

Linear Regression에서는 hypothesis(가설)을 직선의 방정식 Wx + b로 설정하는 데요. 이때 최적의 가설을 찾기 위해 가설과 실제 데이터 간의 차이가 최소인 W와 b를 구하면 됩니다.



한줄로 요약하자면, H(x)라는 모형을 설계하는데 training 데이터로 하여금 Weight(W)와 bias(b)를 알맞게 조정하는것이 목표입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59034478-19c80b80-88a6-11e9-9f7e-b409e0348301.png" width="100%"></center>
Weight와 bias를 알맞게 조정하기 위해서 우리는 Cost 함수라는것을 정의 합니다.

Cost 함수(Loss 함수라고 부르기도 함)는 우리의 모델이 잘 조정이 되는지 평가하는 함수입니다.

Cost 함수는 가설과 실제 데이터 간의 차이를 나타내는 함수입니다.

Cost함수는 우리의 모델이 잘 예측을 하였을 시 값이 줄어들고, 잘못 예측했을시 커지는 특징을 가지고 있습니다.

이러한 특징에 따라 Cost함수를 최소화 시키는것이 우리의 목표입니다.

보통 Cost(x, y) = H(x) -y 와 같은 꼴로 표현 됩니다.

우리의 모델은 Cost 함수를 최소화하는 방향으로 계수인 W를 계속 업데이트 해줍니다.

> Cost 함수를 최소화 하는것이 목적

<center><img src="https://user-images.githubusercontent.com/31475037/59034482-1a60a200-88a6-11e9-84c9-7b8dda913e15.png" width="100%"></center>
> W값을 계속 업데이트 해줌

<center><figure class="second">
	<img src="https://user-images.githubusercontent.com/31475037/59034483-1a60a200-88a6-11e9-88a5-7a5b007cc497.png" width="49%">
	<img src="https://user-images.githubusercontent.com/31475037/59034485-1af93880-88a6-11e9-85d8-9f28aa5239b3.png" width="49%">
</figure></center>
그렇다면 W함수값은 얼마나, 어떻게 업데이트를 해줘야 할까요?

바로 다음과 같이 Cost함수를 W에 대해 편미분해준 뒤 하이퍼 파라미터인 α를 곱해준다음 W에서 빼준것이 업데이트 된 W입니다.
여기서 하이퍼 파라미터란 모델이 자동으로 학습하는것이 아닌 사용자가 정의하는 변수를 의미합니다.

> Cost함수 최적화의 실제 적용

<center><img src="https://user-images.githubusercontent.com/31475037/59034486-1af93880-88a6-11e9-9192-a0a3ff4391ae.png" width="80%"></center>
만약 W가 스칼라값이 아닌 벡터의 형태를 띄어 [w1, w2]와 같은 형태를 가지고, 이를 Loss를 최소화하는 방향으로 업데이트를 하게 된다면 다음과 같은 결과가 나올 수 있습니다.

> w1, w2의 Loss 함수에 따른 업데이트 예시

<center><img src="https://user-images.githubusercontent.com/31475037/59034488-1af93880-88a6-11e9-8c01-5d7972ea642d.png" width="90%"></center>
위에 정의된 Loss 함수는 Convex 형태의 함수이며 Loss 함수의 형태가 Convex해야지 Global minima에 도달할 수 있습니다. Global minima에 도달하는 것을 convergence(수렴)한다고 합니다.

여기서 Convex는 그림2. 와 같은 모양을 지닌 함수를 말합니다.

만약 Loss함수가 Convex하지 않으면(그림1. 이나 그림3. 의 형태) Global minima에 도달 할 수 없습니다. 

> Convex, Concave

<center><img src="https://user-images.githubusercontent.com/31475037/59034491-1cc2fc00-88a6-11e9-845b-ac4774396a3c.png"></center>
또한 Local minima와 Global minima는 함수에서 다음 그림과 같은 지점을 말합니다. 

> Local minima와 Global minima

<center><img src="https://user-images.githubusercontent.com/31475037/59034492-1cc2fc00-88a6-11e9-9a3b-af150a4460e7.png"></center>
<br>

## Multi Variable

> 입력 데이터가 Multi Variable일때는?

이전까지는 입력 데이터가 스칼라의 형태를 지닌 경우를 가정했습니다.

하지만 입력이 만약 벡터라면 어떻게 할까요?

<center><img src="https://user-images.githubusercontent.com/31475037/59034493-1d5b9280-88a6-11e9-9a3e-a5000870ac14.png"></center>
스칼라 값으로 표현되던 것들이 벡터로 표현되면 다음과 같이 수식이 전개 됩니다.

> 스칼라 값으로 표현되던 W, x 둘다 벡터의 형태로 표현

<center><img src="https://user-images.githubusercontent.com/31475037/59034494-1d5b9280-88a6-11e9-8bb4-71e90de0c79b.png" width="80%"></center>
이렇게 벡터의 형태로 표현되는 데이터들을 다루기 위해서는 Matrix multiplication(행렬곱)에 대해서 알아야 합니다.

행렬곱은 행렬 끼리의 곱셈으로 아래 그림과 같이 dot product로 정의되고, dot product를 통해 스칼라 값이 나오게 됩니다.

> Matrix multiplication

<center><img src="https://user-images.githubusercontent.com/31475037/59034496-1d5b9280-88a6-11e9-9995-1618f5ac3be7.png" width="90%"></center>
이런 행렬의 특징을 이용해 W는 weight들의 벡터, X는 훈련 데이터들을 모아 행렬의 형태로 만들어 표현해줍니다.

각 훈련 데이터가 3개의 정보를 가지고 있고, 이런 훈련데이터가 n개 있을시 다음과 같이 표현됩니다.

> Matrix 형태의 표현

여기서 Tensor라는 개념이 나오는데 Tensor는 다차원 배열을 뜻하며,  Tensor의 모양을 shape라 부릅니다.

예를 들어 아래 그림과 같은 [n, 3] 모양의 Tensor가 존재하면 shape가 [n, 3]이라고 부릅니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59034497-1d5b9280-88a6-11e9-8437-8eae0bc0cc00.png" width="90%"></center>
> Tensor 개념

아래와 같이 차원에 따라 각기 다른 이름으로 불리기도 합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59034498-1df42900-88a6-11e9-9999-4c3b9035ff68.png" width="600" Height="400"></center>
<br>

## Logistic Regression

이때까지 배운 Linear Regression을 이용해 시험시간에 따른 시험 합격/불합격 여부를 판단해봅시다.

> Linear Regression 기법을 분류 문제에 적용

하지만 그림과 같이 분류문제를 제대로 풀어내지 못합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59156726-23868480-8adb-11e9-8305-8ae968683f6b.png" width="90%"></center>
이런 문제를 해결하기 위해 나온것이 바로 Logistic Regression 입니다.

Logisitc Regression은 Regression이지만 Classification의 성격을 가져 Classification 기법으로 분류됩니다.

Logistic 함수 모형을 써 regression을 합니다. logistic 함수는 sigmoid 함수라고 불리기도 합니다.

logistic 함수는 x값으로 어떤 값이든 받을 수가 있지만 출력 결과는 항상 0에서 1사이 값이 됩니다. 즉 확률의 형태로 결과 값을 내놓습니다.

> 새로운 모형의 함수를 사용

<center><img src="https://user-images.githubusercontent.com/31475037/59034502-1e8cbf80-88a6-11e9-87b0-e28d9ee426f0.png" width="90%"></center>
Logistic Regression을 부르는 다른 이름은 binary classification입니다.

<br>

**읽어볼 만한 글**

 [regression](https://ratsgo.github.io/machine%20learning/2017/04/02/logistic/)

[Linear Regression 코드](https://github.com/chacha95/Deeplearning_example/blob/master/02.Linear_Regression.ipynb)

[회귀의 어원](<https://m.blog.naver.com/PostView.nhn?blogId=wjn21&logNo=220993310138&proxyReferer=https%3A%2F%2Fwww.google.com%2F>)

<br>

