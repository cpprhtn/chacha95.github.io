---
layout: post
title: Training Neural Net2
tags: [ML]
use_math: true
---

[Optimization](https://chacha95.github.io/2018-11-17-Deeplearning1.5/)글을 먼저 읽으시면 이해에 도움이 많이 됩니다. 

# Optimizer

Neural Network의 weight들을 업데이트 하면서 우리는 Loss 함수의 값을 최소로 만드는 것이 우리의 목표입니다.

> Loss 함수를 최소화 시키는 것이 목표

<center><img src="https://user-images.githubusercontent.com/31475037/59034488-1af93880-88a6-11e9-8c01-5d7972ea642d.png" width="90%"></center>
그렇다면 어떻게 weight들을 업데이트 할까요?

가장 기본이 되는 방식은 Gradient Descent입니다. 

전체 batch를 가져와 Loss 함수를 계산하는 방식입니다. 

> weight 업데이트

weight 업데이트는 크게 두 부분으로 나뉩니다.

첫번째 부분은 learning rate로 사용자가 지정해주는 값이며 보통 0.0001이나 0.0003 값을 많이 씁니다.

두번째 부분은 gradient 계산 부분으로 어떤 방식으로 gradient를 계산하는지에 대한 부분이다.

> weight update 방식

![](https://user-images.githubusercontent.com/31475037/60073926-b02c7600-975c-11e9-8366-62c147e3bfb3.png)

> learning rate에 따른 차이

적절한 learning rate를 설정해 주어야 weight가 잘 업데이트 되고, 잘 업데이트 된 weight를 바탕으로 loss를 최소화 시킬 수 있습니다.

<center><img src="https://t1.daumcdn.net/cfile/tistory/999A143359D86C022F"></center>
**SGD**

SGD는 전체 batch 중 일부만을 가져와 Loss 함수를 계산하는 방식입니다.

그 가져온 일부를 mini-batch라 부릅니다.

SGD를 수식으로 간단히 표현하면 다음과 같습니다.
$$
w_{t+1} = w_t - \alpha \nabla f(w_t)
$$

**Momentum**

Momentum 방식은 Gradient Descent를 통해 이동하는 과정에 관성을 더한 것 입니다. 이전에 이동했던 방향을 기억하면서 움직입니다.
$$
v_{t+1} = \rho v_t + \nabla f(w_t) \ (\rho=0.9)
$$

$$
w_{t+1} = w_t - \alpha v_{t+1}
$$

> Momentum을 이용해 local minima를 빠져나오는데 도움을 받음

기존의 SGD를 이용할 경우 local minima에 빠지면 gradient가 0이 되어 더이상의 업데이트가 힘들지만, momentum 방식의 경우 기존 이동했던 방향으로의 관성을 업데이트 과정에서 더해주게 되어 이런 결과를 냅니다.

<center><img src="http://www.yaldex.com/game-development/FILES/17fig09.gif"></center>
> Momentum을 이용한 Oscilation(진동) 현상 극복

SGD를 이용해 loss가 중앙의 최저점으로 이동해야 하는 상황인데, Oscilation 현상으로 인해 제대로 weight 업데이트가 되지 않고 있다.

이럴 때 momentum을 이용하면 이전에 이동한 방향으로 관성이 생기게 되 Oscilation을 극복 하게 된다.

<center><img src="https://tensorflowkorea.files.wordpress.com/2017/03/ec8aa4ed81aceba6b0ec83b7-2017-03-21-ec98a4ed9b84-3-22-52.png?w=625"></center>
**Nesterov Momentum**

Momentum 방식에 기초를 두었지만, gradient 계산 방식이 다릅니다.

Momentum 방식은 이동 벡터 v를 계산할 때 현재 위치에서의 gradient와 momentum step을 독립적으로 계산하고 합칩니다.

반면 Nesterov에서는 momentum step을 먼저 고려하여, momentum step을 먼저 이동했다고 가정 한 후, 그 자리에서 gradient를 구하는 방식입니다.

Nesterov Momentum을 이용할 경우, 일단 Momentum으로 이동을 한 후 gradient 만큼 이동합니다. 

Nesterov Momentum을 이용할 경우, Momentum 방식에 비해 여러 이점이 있습니다.

Momentum 방식의 경우 멈춰야 할 시점에서도 Momentum에 의해 더 멀리 갈수도 있지만, Nesterov Momentum의 경우 Momentum 방식의 경우 Momentum 만큼의 이동을 한 후 이동을 얼마나 더 할지 결정하기에 좀 더 안정적으로 학습이 됩니다.

![](http://cs231n.github.io/assets/nn3/nesterov.jpeg)

**AdaGrad(Adaptive Gradient)**

AdaGrad는 변수들을 update 할 때 각각의 변수 마다 learning rate를 다르게 설정해서 이동하는 방식입니다.

지금까지 많이 변화하지 않은 변수들은 learning rate를 크게 하고, 지금까지 많이 변화했던 변수들은 learning rate를 작게하는 방식입니다.

단점으로는 학습을 계속 진행하면 learning rate가 너무 줄어든다는 문제가 있어  weight 업데이트가 거의 되지 않게 됩니다.



**RMSProp**

Adagrad의 단점을 해결하기 위한 방법이며, Adagrad의 식에서 gradient의 제곱값을 더해나가면서 구한 G 부분을 합이 아니라 지수 평균으로 바꾸어서 계산해줍니다.

이렇게 대체를 할 경우 Adagrad처럼 G가 무한정 커지지는 않으면서 최근 변화량의 변수간 상대적인 크기 차이는 유지할 수 있습니다.



**Adam**

RMSProp와 Momentum 방식을 합친 알고리즘입니다.

Momentum 방식과 유사하게 지금가지 계산해온 기울기의 지수평균을 저장, RMSProp와 유사하게 기울기의 제곱값의 지수평균을 저장합니다.

가장 대중적으로 사용되는 방식입니다.

<br>

> Saddle point에서의 optimizer 성능

<center><img src="http://i.imgur.com/NKsFHJb.gif?1"></center>
> Long Valley 에서의 optimizer 성능

<center><img src="http://i.imgur.com/2dKCQHh.gif?1"></center>

> Optimizer 발전 과정

<center><img src="https://image.slidesharecdn.com/random-170910154045/95/-49-638.jpg?cb=1505089848"></center>

학습식을 보면 수정할 수 있는 부분이 learning rate와 gradient 부분입니다.

- gradient를 수정한 momentum, Nag
- learning rate를 수정한 Adagrad, RMSProp, AdaDelta
- 이 두 종류의 장점을 합한 Adam, Nadam

<br>

**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

**읽어볼만한 글**

[optimization algorithms](http://sebastianruder.com/optimizing-gradient-descent/)

[optimization 2](https://tensorflow.blog/2017/03/22/momentum-nesterov-momentum/)

<br>

