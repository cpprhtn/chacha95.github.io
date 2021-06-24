---
layout: post
title: Optimization
tags: [ML]
use_math: true
---

이번 포스트에서는 weight들이 어떻게 조정 되는지 알아 봅시다.

편의상 weight와 bias를 weight라고 묶어서 말하겠습니다.

[미분](https://chacha95.github.io/2018-11-01-numerical/)포스트를 읽고, CS231n Backpropagation강의를 듣고 읽으시는 걸 추천드립니다!

(Nerual Network에 있어 가장 중요한 부분입니다)

<br>

## Loss function

일반적으로 Loss 함수는 다음과 같이 정의됩니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59832157-3cfeba80-937e-11e9-8862-aaff57cff62f.png"></center>
## How to optimize loss fuction?

그렇다면 어떻게 하면 loss 함수를 통해 weight들을 업데이트 할 수 있을까요?

보통 weight를 업데이트 한다고 할 때 드는 비유로, 경사진 산 길을 따라 내려간다고 비유합니다.

왜냐하면 Loss함수가 Convex하다고 가정을 하고 Loss 함수를 따라 global minima에 도달하는 것이 비슷하기 때문입니다.

가장 먼저 간단하게 생각해 볼 수 있는 것은 random하게 weight를 업데이트 해나가는 것입니다. 하지만 이 방식은 결과가 굉장히 안좋습니다. 이는 경사진 산 길을 따라 내려갈 때 눈을 가리고 내려가는 것과 비슷한 것입니다.

두번째로 생각해 볼 방법은 Slop를 따라가는 방식입니다. 여기서 Slop란 gradient를 의미하며, gradient를 따라 간다는 의미입니다.

왜냐하면 gradient의 반대방향이 가르키는 방향이 각 지점에서 함수의 출력 값을 가장 크게 줄이는 방향이기 때문입니다.

즉, Loss 함수를 최소로 만들기 위해서 각 지점에서의 gradient의 반대 방향으로 가는것이 최선의 선택입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59837836-e2b72700-9388-11e9-8cc3-60686431b782.png" width="100%"></center>
> 1차원 gradient 계산

1차원의 gradinet를 구하는 식은 다음과 같습니다.

![](https://user-images.githubusercontent.com/31475037/59837838-e2b72700-9388-11e9-9ac5-d8e48c31a0a1.png)

1차원 gradient 식을 이용해 W를 업데이트 해볼까요?

> numerical gradient

numerical한 방식을 사용해 gradient를 구하면 다음과 같습니다. 

엄청 작은 h값(0.0001)을 설정, 그만큼의 값을 업데이트 해 numerical한 gradient를 구합니다. 

![](https://user-images.githubusercontent.com/31475037/59837840-e2b72700-9388-11e9-8c44-8615ca1de2d6.png)

> analytic gradient 

analytic gradient는 실제로 미분공식을 이용하여 편미분 값을 바로 구할 수 있게 해줍니다.

![](https://user-images.githubusercontent.com/31475037/59913237-bf58ae80-9452-11e9-9978-71111e0ebfea.png)



두 기법을 비교한다면 다음과 같습니다.

정리해서 말하자면 numerical은 근사치이고 느리다. 반면에 코드로 작성하기 쉽습니다.

그리고 analytic은 빠르고 정확한데 에러가 늘어날 경우가 많습니다.

결론적으로 말하면 다 analytic  gradient를 쓰면 됩니다.

그리고 중간중간에 gradient 계산이 잘 되고 있는지 numerical gradient를 이용해 확인해줍니다.(이를 gradient check라고 부름)

<br>

## Stochastic Gradient Descent

이렇게 구해진 gradient를 따라 weight들을 업데이트해 나가면 loss 함수의 global minima에 도달 할 수 있게 됩니다.

아래 그림에서 흰색 부분이 처음 주어진 weight 벡터이고, 해당 지점에서 gradient를 구한 뒤 gradient를 따라 내려가는 모습입니다. 

<center><img src="https://user-images.githubusercontent.com/31475037/59034488-1af93880-88a6-11e9-8c01-5d7972ea642d.png" width="90%"></center>
### Stochastic Gradient Descent

이 때 까지 우리가 봤던 방식은 training set 전체를 이용해 학습을 하는 full gradient descent 였다면, 앞으로 우리가 사용할 방식은 mini-batch를 이용한 Stochastic Gradient Descent입니다.

training set 전체를 batch라고 했을 때, training set 일부를 가져온 것을 mini-batch라고 합니다. 이 mini-batch만을 이용해 gradient를 계산하고 이를 통해 weight를 업데이트 해줍니다.

이 과정이 끝나면 이전 mini-batch와 겹치지 않는 mini-batch를 하나 가져와 다시 학습합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59914868-4c513700-9456-11e9-9e24-8247ff49d458.png" width="90%"></center>
<br>

## Backpropagation(역전파)

Backpropagation은 Neural Net이 구체적으로 어떻게 학습하는지에 대한 방법론입니다. 

Backpropagation은 추상적이기에 Neural Net의 학습 과정이 마법처럼 데이터를 학습해서 결과를 만든다고 생각할 수 있습니다. 

하지만 Image Net task와 같이 잘 만들어진 모델과 학습데이터를 벗어나, 실제 현장에서 이를 적용하려 하면 학습이 이상적으로 되지 않고, gradient vanishing , dying ReLU와 같은 중대한 결점이 발생합니다. 

따라서 우리는 Backpropagation에 대해 완전히 이해해야 하며 어떻게 Weight가 업데이트 되는지 잘 알아야 합니다.

Weight가 gradient를 따라 update 되면서 학습이 된다 하는데, 그렇다면 gradient는 어떻게 구할 까요?

그 gradient를 구하는 방법으로 Backpropagation이 제안되었습니다.



**간단한 예시**

다음과 같은 간단한 예시를 들어 봅시다.

입력 값이 x, y, z 세 개이고, 결과값이 f인 예시입니다. 우리가 원하는 것은 x, y, z가 f에 미치는 변화율 입니다. 따라서 각 변수 별로의 편미분을 통해 값을 구해줍니다.  
$$
\frac {\partial f} {\partial x}, \ \frac {\partial f} {\partial y},\ \frac {\partial f} {\partial z}\ 를 구하는\ 것이 \ 최종 목표입니다.
$$
> 예시

![](https://user-images.githubusercontent.com/31475037/59961339-61d76700-9511-11e9-899c-66046a1172de.png)



먼저 z가 함수 f에 대한 영향을 알기 위해서 편미분을 해봅시다. 

f와 z 함수는 직접적으로 연결되어 있기 때문에 f를 z에 대해서 편미분을 하면 바로 구할 수 있습니다. 결과적으로는 3의 값이 나오게 됬습니다.

> f와 직접적으로 연결되어 있어 바로 f를 z로 편미분한 값을 구할 수 있음

![](https://user-images.githubusercontent.com/31475037/59964083-dc66ad80-9536-11e9-8328-275b6c3e66fc.png)



그렇다면 f를 x와 y로 편미분 하려면 어떻게 해야 할까요?

먼저 f와 x, y는 직접적으로 연결되어 있지 않기 때문에 두번의 편미분을 거쳐야 합니다.

q = x + y이기에, f를 q에 대해서 편미분 한 값을 먼저 구해봅시다.

> f를 q로 편미분한 값을 먼저 구함

편미분을 통해 f를 q로 편미분한 값이 -4인 것을 알아냈습니다!

![](https://user-images.githubusercontent.com/31475037/59964084-dc66ad80-9536-11e9-83b0-48d6352b9a1c.png)

구해진 편미분 값에 대해서 어떤 방법을 통해서 우리가 원하는 값을 얻을 수 있을까요?

바로 chain rule이라는 법칙을 이용해 구할 수 있습니다.

> chain rule의 적용

$$
\frac {\partial f} {\partial x} = \frac {\partial f} {\partial q} \frac {\partial q} {\partial x}
$$

$$
\frac {\partial q} {\partial x}은 \ q=x+y를 \ 편미분하면 \ \frac {\partial q} {\partial x}이 \ 1인것을 \ 알 \ 수 \ 있습니다.
$$

이를 chain rule에 적용해 보면 우리가 원하는 값을 얻을 수 있습니다.

> q를 x와 y로 편미분한 값을 구함

![](https://user-images.githubusercontent.com/31475037/59961340-61d76700-9511-11e9-9a9a-a83cca42ca09.png)

chain rule을 적용함으로써 우리가 원하는 f를 특정 변수로 편미분한 값, 즉 f의 결과값에 특정 변수가 얼마나 영향을 미치는지에 대해 알게 되었죠?

예를 들어 loss함수 f가 있을 때 f에 특정 변수 x가 얼마나 영향을 미치는지 알기 위해서 loss 함수로부터 backpropagation를 통해 거슬러 올라가 계산하면 알 수 있습니다. 

이를 기반으로 weight를 업데이트 합니다.

그렇다면 실제 Sigmoid가 들어간 예시를 풀어볼까요?

**Sigmoid가 들어간 예시**

편의상 p1, p2와 같은 예비 변수명을 붙였습니다.

아래 그림의 빨간 박스친 부분이 해당 노드의 미분 공식입니다.

> backpropagation 진행

간단한 예시에서 쓰였던 방법을 그대로 따라 진행하시면 됩니다!

![](https://user-images.githubusercontent.com/31475037/59964859-821f1a00-9541-11e9-815c-14cca540b542.png)



![](https://user-images.githubusercontent.com/31475037/59964860-821f1a00-9541-11e9-89a3-036934d9ca8f.png)



![](https://user-images.githubusercontent.com/31475037/59964861-821f1a00-9541-11e9-964e-45e1d501772a.png)



![](https://user-images.githubusercontent.com/31475037/59964862-82b7b080-9541-11e9-94d9-7fb64592ec96.png)



![](https://user-images.githubusercontent.com/31475037/59964863-82b7b080-9541-11e9-8dee-07eb6f332660.png)



![](https://user-images.githubusercontent.com/31475037/59964864-82b7b080-9541-11e9-8e54-3256aa6e94ab.png)



> gradient를 거슬러 올라가 weight들을 업데이트 해준다!

결국 weight는 [local gradient] x [(upstream) gradient] 연산에 의해 업데이트 됩니다.

![](https://user-images.githubusercontent.com/31475037/59961352-76b3fa80-9511-11e9-94c1-86c98d34c020.png)



<br>

**읽어볼만한 글**

[Backpropagation](https://medium.com/@karpathy/yes-you-should-understand-backprop-e2f06eab496b?fbclid=IwAR0pB_YOUdMvXoLWsYyEwMp3MWxRZyadd24zxorvnAPkKXK7flbkGWaQyh8)

[Backpropagation2](<http://taewan.kim/post/backpropagation_matrix_transpose/?fbclid=IwAR1rmwOmNpYwnDNuAF8Gxv-wmWFoZ_Paiplt9u5lysr3Yk26-5u_uAd3Www>)

[Backpropagation3](http://jaejunyoo.blogspot.com/2017/01/backpropagation.html)

[Interactive Demo](https://www.deeplearning.ai/ai-notes/)

**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

<br>





