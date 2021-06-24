---
layout: post
title: Training Neural Net
tags: [ML]
use_math: true
---

# Training Neural Network

## Data Set Preprocessing

데이터 행렬 X에 대해서 일반적으로 아래의 3가지 전처리 방식을 사용합니다.

**Mean Subtraction**

가장 흔하게 사용되는 데이터 전처리 기법입니다. 데이터의 모든 feature의 각각에 대해서 평균값 만큼 차감하는 방법으로 기하학 관점에서 보자면 데이터 군집을 모든 차원에 대해서 원점으로 이동시키는 것으로 해석할 수 있습니다.

**Normalization**

Normalization은 각 차원의 데이터가 동일한 범위내의 값을 갖도록 하는 전처리 기법을 의미합니다. 

일반적으로 다음 2가지 중 하나를 선택하여 구현합니다.

- 각 데이터 값을 평균 만큼 차감하고 표준 편차 값으로 나눕니다.
- 데이터 값의 범위를 [-1, 1]로 만듭니다. 하지만 이 기법은 scale이 동일해야지 feature 학습에 도움이 줄거라는 가정하에 사용하는 것이 일반적입니다. 이미지 처리에서는 각 픽셀 값이 이미 동일한 scale(0~255)을 갖고 있는 경우가 대부분이기에 사용 하는 경우도 있고 안하는 경우도 있습니다.(e.g. pretrained VGG net은 보통 [-1, 1]로 정규화 되어있다.)

![](http://aikorea.org/cs231n/assets/nn2/prepro1.jpeg)



**PCA와 Whitening**

먼저 Mean Subtraction 기법을 이용해 데이터를 정규화 시킵니다. 그리고 Covariance를 계산합니다.

PCA를 하고난 데이터는 데이터간에 decorrelated됩니다.

Whitening은 eigenbasis(기저벡터) 데이터를 eigenvalue(고유값) 값으로 나누어 정규화 하는 기법입니다. 

Whitening 변환은 기하학적 해석은, 입력 데이터가 multivariable gaussian 분포를 따르면 whitening된 데이터는 평균은 0이고 Covariance는 단위행렬을 갖는 정규분포를 갖게됩니다.

> 중앙은 PCA를 적용한 데이터 분포, 오른쪽 그림은  whitening 기법을 적용한 데이터 분포

![](http://aikorea.org/cs231n/assets/nn2/prepro2.jpeg)

<br>

## Loss와 Regularization

일반적으로 Cost(Loss) 함수는 다음과 같이 구성됩니다.

> Loss 함수

Distance를 이용해 정답과 Neural Net을 통해 나온 결과값을 비교하는 부분과 Generalization을 위해 쓰이는 Regularaization 부분입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59832157-3cfeba80-937e-11e9-8862-aaff57cff62f.png"></center>

**L1 Loss**

A에서 B로 이동 할 때 각 좌표축 방향으로만 이동할 경우 계산되는 거리입니다.

실제 값과 예측값의 그 차이값에 절대값을 취하는 방식입니다.

다른 말로는 Least Absolute Deviations 줄여서 LAD라고도 불립니다.
$$
L_1 = \sum_{i=1}^n \left| y_i - f(x_i) \right |
$$

<center><img src="http://i.imgur.com/hXR6RFw.png" width="70%"></center>

**L2 Loss**

두 관측치 사이의 직선 최단거리를 의미합니다.

Mean Squared Error(MSE)라고도 불리며, 실제값과 예측값의 차이값에 제곱을 하는 방식입니다.
$$
L =\sum_{i=1}^n(y_i - f(x_i))^2
$$

<center><img src="https://user-images.githubusercontent.com/31475037/59828966-2acd4e00-9377-11e9-8350-8e1cbf0de444.png"></center>



> L1과 L2 비교

아래 그림에서 L1과 L2의 특성에 대한 비교를 시각화 하였습니다.

L1과 L2는 다음과 같은 차이가 있습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59672849-40b60400-91fb-11e9-9ddd-9ff58873bf03.png"></center>
**Cross-Entropy**

[Softmax](https://chacha95.github.io/2018-11-09-Softmax/)글에 잘 설명해 두었습니다.

<br>

### Regularization

Overfitting을 방지하는 가장 확실한 방법은 Training 데이터를 늘리는 일이지만, 추가 데이터 확보가 어렵거나 불가능한 경우가 많습니다.

그러한 상황일때 Regularization 기법을 사용하면 Generalization 성능을 높이는데 도움이 됩니다.

weight decay 기법이라고도 불립니다.



**L1 Regularization(L1 Norm)**

lasso regression이라고도 합니다.

기존의 loss의 가중치의 크기가 포함되면서 가중치가 너무 크지 않은 방향으로 학습되도록 합니다.

모든 weight의 절댓값 만큼의 크기를 loss 함수에 제약을 거는 방식으로 구현됩니다.
$$
\lambda R(w) = {\frac 1 2}\lambda\left | w \right |
$$

**L2 Regularization(L2 norm)**

ridge regression 이라고도 불립니다.

가장 일반적으로 사용되는 방식입니다. 모든 weight의 제곱 만큼의 크기를 loss 함수에 제약을 거는 방식으로 구현됩니다.


$$
\lambda R(w) = {\frac 1 2} \lambda \left | w \right |^2
$$


**L1, L2 Regularization**

L1, L2 Regularization 부분을 추가한다는 의미는, 가중치 w가 작아지도록 학습한다는 것입니다. 이는 local noise와 outlier의 영향을 더 적게 받도록 하겠다는 것입니다.



> lamda값에 따른 변화

lamda값이 커짐에 따라 Generalization 능력이 증가하고 있습니다.

![](http://cs231n.github.io/assets/nn1/reg_strengths.jpeg)

**Dropout**

각 뉴런들을 p의 확률로 활성화 시켜 학습에 적용하는 방식입니다.

Neural Net의 모든 뉴런을 학습에 참여시키는 것이 아니라, 뉴런 중 일부를 학습 과정 중에 꺼버려 일부만 학습시키는 방식입니다.(전체 weight의 일부만을 사용하여 학습을 시킴)

<center><img src="http://aikorea.org/cs231n/assets/nn2/dropout.jpeg"></center>

<br>

### Data Set

**Data Set 구성**

> 데이터셋 구성

일반적으로 Data Set은 Training/Validation/Testing으로 나뉘어 집니다.

여기서 **Test Set은 절대로 Training에 쓰이면 안됩니다**

Data Set을 이렇게 나누는 이유는 Test Set이 모델의 Training과정 중에 쓰이는 것을 막기 위해 Training 과정이 잘 되고 있는지 아닌지 판단하는 지표가 loss 밖에 없습니다. 하지만 loss가 낮다고 해서 Test Set에서의 결과가 항상 좋은것만은 아닙니다.(그 이유는 아래서 설명)

그렇기 때문에 Training 과정 중에 모델에 대해 평가를 하기 위한 Validation Set을 만드는 것입니다. 



<center><img src="https://t1.daumcdn.net/cfile/tistory/9951E5445AAE1BE025"></center>
**k-fold cross validation**

k-fold cross validiation 기법은 data set의 수가 적을 때 정확도를 향상시키는 방법입니다. 

방법은 다음과 같습니다.

- Training을 k개의 fold로 나눕니다.
- 아래는 5개의 fold로 나누었을 때
- 한개의 fold에 있는 데이터를 다시 k개로 쪼갠 뒤, k-1개는 Train Set, 마지막 한개는 Validation Set을 지정합니다.
- 다음 fold에서는 Validation Set을 바꿔 지정하고, 이전 fold에서 Validation 역할을 했던 Set은 다시 Training Set으로 활용합니다.
- 이를 K번 반복합니다

이러한 방법을 이용하여 적은 Training Set을 크게 만들 수 있습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59551217-06592680-8fb1-11e9-9adf-c8a0592101b2.png"></center>
**Overfitting과 Training**

Training data에 너무 overfitting하게 되버리면 오히려 Test에서의 성능이 크게 떨어집니다.

>  그렇다면 언제까지 학습을 해야 하는 것일까?

<center><img src="https://user-images.githubusercontent.com/31475037/59551074-022c0980-8faf-11e9-9466-5f242652d0a6.png"></center>
학습시킨 모델을 Validation에 놓고 조금씩 비교하다보면 초반 loss는 떨어지나, 어느 시점부터 loss가 증가하는 것을 보입니다. 결국 Training을 무조건 지속하기보다는 Validation의에러가 증가하는 시점에 학습을 중단하는 것이 좋습니다.

즉, 모델을 한번 학습 시켰다면 그것을 Validation Set으로 Error가 얼마나 내려가는지 한번 더 확인해 보는 것입니다. Validation Set이 최적의 Error를 보인다면 그 지점이 가장 학습이 잘 된 지점으로 생각하고 학습을 중단 시키면 됩니다.

그리고 Test Set으로 실제 모델을 평가 하면 됩니다. 



<br>

### Gradient Clipping

neural net에서 gradient가 너무 작거나 크게되는 것을 방지하는 기법입니다. 다양한 방법이 존재하지만, 흔히 쓰이는 방법은 gradient의 L2 norm이 기준값을 초과할때(threshold /L2 Norm)을 곱해주는 것입니다. 흔히 RNN 계열에서 많이 쓰입니다.

Gradient에 문제가 생기는 경우는 다음과 같은 경우가 대표적입니다.

> 가파른 gradient를 가져 gradient exploding 현상을 초래

<center><img src="https://t1.daumcdn.net/cfile/tistory/99487A3359E302CE2E" width="80%"></center>
이러한 문제에 대처하는 한가지 방법은 learning rate를 매우 작게 설정하는 것입니다. 

이 솔루션은 학습 속도를 매우 느리게 만들고, 잘못하면 local minima에 빠지게 만듭니다. 위의 솔루션보다 더 좋은 솔루션은 gradient clipping을 사용하는 것입니다.

gradient clipping의 개념은 gradient가 최대치를 넘게되면 gradient의 크기를 재조정해서 gradient의 크기를 조정하는 것입니다. 

이러한 gradient clipping은 최적화 알고리즘이 가야하는 방향은 그대로 유지하면서 업데이트되야하는 gradient의 크기를 자동으로 조절해줍니다.

<br>



**읽어볼만한 글**

[Gradient Clipping](http://nmarkou.blogspot.com/2017/07/deep-learning-why-you-should-use.html)

**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

<br>

