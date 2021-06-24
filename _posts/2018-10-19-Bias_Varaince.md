---
layout: post
title: Bias and Variance
tags: [ML]
---

# Bias and Varaince Trade-off

machine learning의 학습에 쓰이는 error(loss) 함수는 다음과 같이 분리 될 수 있습니다.

> noise는 데이터가 가지는 본질적인 한계치이기 때문에 irreducible error라 불리며, bias/variance는 모델에 따라 변하는 것이기에 reducible error라 불립니다

<center><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile6.uf.tistory.com%2Fimage%2F991B6333599AEF3D1349E8"></center>
여기서 error에 큰 영향을 미치는 bias와 variance의 상관관계에 대해 알아봅시다.

bias variance trade-off란 bias와 variance 둘다 target에 맞추고 싶지만, 양립할 수 없는 성질입니다.

bias variance trade-off는 지도학습(supervised learning)에서의 error 핸들링에서 가장 중요한 부분 중 하나입니다.

**bias**는 잘못된 추정이나 overly-simplistic한 추정에의해 생기는 error 입니다. bias에 의해 학습 과정 중에 데이터에 대해 under-fitting하는 결과물을 만듭니다. high bias는 우리의 학습 알고리즘이 feature를 제대로 학습하지 못한다는 것을 의미합니다. 

**variance**는 overly-complex하게 training 데이터에 대해 fitting하려 하는 것을 의미합니다. high variance에서 모델은 training data에 대해 over-fitting하는 경향이 있습니다. 즉 데이터 내에 있는 에러나 노이즈까지 잘 잡아내는 highly flexible models에 데이터를 fitting시킴으로서, 실제 현상과는 관계없는 random한 것들까지 학습하는 경향이 있습니다. 

bias는 트레이닝 데이터를 바꿈에 따라서 알고리즘의 평균 정확도가 얼마나 많이 변하는지 보여주고, variance는 특정 입력 데이터에 대해 알고리즘이 얼마나 민감한지를 나타냅니다. variance는 loss를 의미 하지만, 참 값과는 관계없이 추정값들의 흩어진 정도만을 의미합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59551500-8bded580-8fb5-11e9-8bff-c06a777645e4.png"></center>
<br>

## Fitting

이상적인 모델은 트레이닝 데이터에서 반복되는 규칙성을 정확하게 잡아내면서도 학습되지 않은(unseen) 데이터를 잘 일반화 할 수 있는 모델입니다.

**선형 모델(degree=1)은 under-fit이다**

이 모델은 데이터 내의 모든 정보를 고려하지 못하고 있습니다.(high bias) 새로운 데이터가 들어온다 하더라도 이 모델의 형태는 크게 변하지 않을 것입니다.(low variance)

**반면에 고차 다항함수 모델(degree=20)은 over-fit이다**

이 곡선 모델은 주어진 데이터를 잘 설명하고 있습니다.(low bias) 이 함수는 새로운 데이터가 들어 왔을 때 완전히 다른 형태로 변하게 되고, generality를 잃게 될 것입니다.(high variance)

**이상적인 모델은 데이터의 규칙성을 잘 잡아내어 정확하면서도 다른 데이터가 들어왔을 때도 잘 일반화 할 수 있는 모델일 것입니다(degree=3)**

이처럼 train error를 줄이기 위해 모델의 복잡도(degree)를  계속 높이면 overfitting이 일어나게 됩니다. 즉 모델 복잡도가 올라갈 수록 bias는 감소하나, variance가 증가하는 현상이 일어나게 됩니다.

실제로 두 가지를 동시에 만족하는 것은 불가능하며, 따라서 training data가 아닌 test data(실제 데이터)에서 좋은 성능을 내기 위해 이런 trade-off는 반드시 생길 수 밖에 없으며 이는 bias variance trade-off라 불립니다.

test 과정에서, high variance를 가진 모델은 제대로 예측을 하지 못합니다. 왜냐하면 training data의 variation의 정도에 너무 민감해져, test단의 prediction에 노이즈가 생겨 잘못된 prediction을 하게 됩니다.(generiazation 능력이 떨어짐)

<br>

## Model Complexity

편향(Bias)과 분산(Variance)는 한쪽이 증가하면 다른 한쪽이 감소하고, 한쪽이 감소하면 다른 한쪽이 증가하는 경향을 보입니다.

모델이 데이터를 반복 학습하는 횟수가 늘어날수록 모델의 복잡도도 따라서 늘어나게 되는데, 이것은 훈련용 데이터를 그대로 외우는 방향입니다. 즉 훈련 데이터에 overfitting이 된다는 의미이고, 이는 bias가 낮아지고 variance가 높아진다는 의미입니다.

이런 문제가 생기게 되면, 학습 도중의 loss 자체는 계속해서 떨어지지만, generalization 능력이 떨어지게 되어 실제 test/validation 단에서의 능력이 떨어지게 됩니다.

그렇기에 training data를 training/validation으로 나누어, 학습도중에 validation error를 측정해 validation error가 최저점인 순간 학습을 그만 두어야 합니다.

> Training loss가 떨어진다고 해서 test/validation loss가 떨어지는 것은 아님

![](https://lh5.googleusercontent.com/lAbzDl1HYiYHAEuGnaUw2GdCyQzkZvjWisgNY-ZRYqvRG-X-U7f7cL_UunIF7v5q0BbUSw4CZ-1-xMXs8mvE8fbGa7ghFeEGzuwJ6wiIs64nUgJxkDNEC2JrSTUHEjViRZLdA23NLqI)

<br>

##### 참고 강의

[Quora]([https://www.quora.com/What-is-the-best-way-to-explain-the-bias-variance-trade-off-in-layman%E2%80%99s-terms](https://www.quora.com/What-is-the-best-way-to-explain-the-bias-variance-trade-off-in-layman's-terms))

<br>

