---
layout: post
title: SVM
tags: [ML]
---

# SVM이란 무엇인가?

Deeplearning 이전에 classification(분류)에서 많이 쓰이던 machine learning(기계학습) 기법입니다. (현재도 특정 분야에선 많이 쓰임)

두개의 클래스가 주어졌을 때 이를 분류하는 것이 SVM 알고리즘입니다. Street라 명명되는 부분의 support vector로 decision boundary(결정 경계)가 결정됩니다. Decision boundary를 기준으로 두 class가 나뉩니다.

> SVM

<center><img src="https://user-images.githubusercontent.com/31475037/59006503-40ae1f80-885d-11e9-8939-3f15846bf0f0.png" width="90%"></center>
<br>

## Margin

SVM에선 크게 두가지의 margin 모델이 존재합니다.

### Hard margin

Hard margin classification은 생성되는 street의 margin을 좁게 두는 방식입니다. 

각 데이터가 클래스 별로 정확하게 분리 되어 있는 경우에 적용할 시 분류를 정확하게 할수 있습니다.  Data가 linearly separable(선형 분리 가능) 할 경우만 사용이 가능합니다. Outlier에 영향을 많이 받습니다.

> outliers에 따른 class 구분

<center><img src="https://user-images.githubusercontent.com/31475037/59006596-c6ca6600-885d-11e9-9ce5-7196c772c434.png"></center>
### Soft Margin

Hard margin classification 보다 robust 합니다.

목표는 Street를 최대한 크게 유지하면서, margin violation을 억제하는것입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59006817-cda5a880-885e-11e9-8d1d-3129011e8bbc.png" width="80%"></center>
이 두 조건의 황금률을 유지를 잘하게 되면 hard margin보다 robust하기에 soft margin이 hard margin 보다 더 많이 쓰입니다.

![](https://user-images.githubusercontent.com/31475037/59006818-ced6d580-885e-11e9-8887-9ed7139ea740.png)

C는 margin을 의미하며 모델이 overfitting(과적합)된거 같으면 C를 감소시켜 margin을 넓혀주면 됩니다.

<br>

## Nonlinear SVM classification

이처럼 SVM의 분류하고자하는 데이터가 linearly separable하다면 좋겠지만, 대다수의 분류문제에서 클래스의 데이터들은 linearly separable하지 않습니다.

그래서 우리는 다음과 같은 방식을 이용해 데이터를 클래스 별로 선형 분류 가능하도록 만들것입니다.

### Polynomial expansion

왼쪽 아래의 그림은 linearly separable하지 못합니다. 이럴때 다음과 같은 feature expansion을 통해 더 높은 차원에서 데이터를 표현합니다. 다음과 같은 예시는 polynomial(다항식)하게 feature expansion을 하는 경우 입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59158317-96035e80-8af3-11e9-859b-8861596ac4f6.png" width="150%"></center>
> 1차원의 데이터를 2차원에서 표현해 linearly separable하게 만듭니다.

![](https://user-images.githubusercontent.com/31475037/59006819-d0080280-885e-11e9-9769-30e808c757e4.png)

Polynomial expansion은 사용이 쉽고, 결과물이 좋습니다. 하지만 차원의 확장을 적게 하면 복잡한 데이터를 다룰 수 없고, 차원의 확장을 높게 하면 복잡한 데이터를 다룰 수 있지만 너무 느리다는 단점이 있습니다.

이러한 단점을 상쇄하는 Kernel trick 방식이 있으며, polynomial expansion과 비슷한 결과물을 얻을 수 있습니다.

<br>