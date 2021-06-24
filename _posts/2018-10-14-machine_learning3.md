---
layout: post
title: K-Nearest Neighbor(KNN)
tags: [ML]
---

# KNN 알고리즘

KNN 알고리즘은 머신러닝의 분류, 회귀에 쓰이는 대표적이면서 간단한 알고리즘 입니다. 개인 영화 추천 등에 활용됩니다.

KNN 알고리즘은 unsupervised learning으로 정답 레이블이 없는 경우의 class를 예측할 때 쓰입니다. 

KNN은 새로운 데이터가 주어졌을 때 기존 데이터 가운데 가장 가까운 k개 이웃의 정보로 새로운 데이터를 예측하는 방법론입니다. 아래 그림처럼 검은색 점의 class 정보는 주변 이웃들을 가지고 추론해 낼 수 있습니다. 즉, 분류하고자 하는 데이터의 k개의 이웃을 구한 뒤, 그 이웃들과의 distance를 계산, 가장 distance 값이 적은 class를 선택합니다. 

만약 k가 1이라면 오렌지색, k가 3이라면 녹색으로 분류하는 것입니다. 

> KNN

![](http://i.imgur.com/gLBo1gX.png)

KNN은 학습이라고 할 만한 절차가 없습니다. 새로운 데이터가 들어왔을 때, 기존 데이터 사이의 거리를 재서 이웃들을 뽑기 때문입니다. 그렇기에 **Instance-based learning**이라고 불리기도 합니다. 데이터로부터 모델을 생성해 훈련시키는 학습방식인 **Model-based learning**과 대비되는 개념입니다.

KNN의 Hyperparameter는 **탐색할 이웃 수(k)**, **거리 측정 방법** 두가지 입니다. k가 작을 경우 데이터의 지역적 특성을 지나치게 반영합니다(overfitting).

반대로 매우 클 경우 모델이 과하게 정규화 되는 경향이 있습니다.(underfitting)

k값이 커짐에 따라 분류 경계면이 단순해지는 것을 확인할 수 있습니다.

여기서 K=1일 때 초록색 영역에 섬처럼 노란색 영역이 존재하는 걸 볼 수 있는데, K값이 커져가면서 알고리즘의 generalization 능력이 좋아져 K=3 이후 부터는 해당 영역이 없어진 것을 확인 할 수 있습니다.

설명드린 초록색 영역에 섬처럼 존재하는 노란색 점을 노이즈가 낀 학습 데이터라고 부릅니다.

> k 값에 따른 분류

![](https://user-images.githubusercontent.com/31475037/59829870-5ea97300-9379-11e9-8978-c7fde1549831.png)

<br>

## distance

KNN의 거리 측정 방법에 따라 그 결과가 달라지는 알고리즘입니다.

### Euclidean Distance(L2 Distance)

두 관측치 사이의 직선 최단거리를 의미합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59828966-2acd4e00-9377-11e9-8350-8e1cbf0de444.png"></center>
### Manhattan Distance(L1 Distance)

A에서 B로 이동 할 때 각 좌표축 방향으로만 이동할 경우 계산되는 거리입니다.

![](https://user-images.githubusercontent.com/31475037/59828968-2acd4e00-9377-11e9-8851-32d6e71ab65d.png)

<center><img src="http://i.imgur.com/hXR6RFw.png" width="60%"></center>
### L1과 L2의 비교

가장 많이 쓰이는 distance인 L1과 L2를 간단하게 나타내면 다음과 같습니다.

![](https://user-images.githubusercontent.com/31475037/59830409-8cdb8280-937a-11e9-9e6d-4e408fa75488.png)

> L1, L2 distance에 따른 결과 차이

![](https://user-images.githubusercontent.com/31475037/59830629-03788000-937b-11e9-9f43-969a2d13715a.png)

K값과 distance에 따른 자세한 차이를 알고 싶으시면 [web demo](http://vision.stanford.edu/teaching/cs231n-demos/knn/)를 한번 해보세요!



### Mahalanobis Distance

변수 내 분산, 변수 간 공분산을 모두 반영하여 거리를 계산하는 방식입니다.

변수 간의 correaltion을 고려한 거리 지표 입니다.

![](https://user-images.githubusercontent.com/31475037/59828969-2acd4e00-9377-11e9-9a65-1840d6e7e04b.png)

<br>

## KNN의 장단점

KNN은 학습 데이터 내 노이즈의 영향을 크게 받지 않으며, 학습 데이터 수가 많다면 꽤 효과적인 알고리즘입니다. 특히 마할라노비스 거리와 같이 데이터의 분산을 고려할 경우 매우 robust한 방법론입니다.

그러나 최적 이웃의 수(k)와 어떤 거리 척도가 분석에 적합한지 불분명해 데이터 각각의 특성에 맞게 연구자가 임의로 선정해야 하느 단점이 있습니다.

또 새로운 관측치와 각각의 학습 데이터 사이의 distance를 전부 측정해야 하므로 계산시간이 오래 걸립니다.

KNN 알고리즘은 이미지 관련 task에서는 쓰이지 않습니다. 그 이유는 다음과 같습니다.

- test time에서 굉장히 오랜 시간이 걸립니다
- pixel에 대한 distance가 informative하지 않습니다.
- 차원의 저주 때문에 고차원 데이터에 대해 잘 작동하지 않습니다(고 해상도 이미지는 고차원이기 때문)



<br>

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

<br>

