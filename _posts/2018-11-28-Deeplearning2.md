---
layout: post
title: Neural Network
tags: [ML]
use_math: true
---

# 어떻게 하면 기계가 학습해서 풀 수 있게 할까?

어떻게 하면 기계가 학습해서 다음과 같은 문제를 풀 수 있을까요? 라는 질문에 답하기 위해 제안된 모델이 바로 Perceptron입니다.

<br>

## Perceptron

> AND NAND OR XOR 문제

처음에 기계가 풀기 원하는 문제는 바로 다음과 같은 문제 였습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59172735-b5969780-8b84-11e9-8d3a-8445513adfc2.png" width="100%"></center>
퍼셉트론은 인공신경망(Artificial Neural Network)의 한 종류로서, 1957년에 코넬 항공 연구소의 Rosenblatt에의해 만들어졌습니다. 이것은 가장 간단한 feedfoward 네트워크로 볼 수 있습니다. (feedforward는 앞으로 순차적으로 feed를 주는 시스템을 말합니다)

Perceptron은 input 벡터 [x1, x2]가 있고 이를 weight 벡터 [w1, w2]와 dot product를 통해 scalar 값만을 보내고, activation function(sigmoid)를 통해 나오는 값이 출력 값이 됩니다.

여기서 기계가 출력 값을 정답으로 배출하도록 weight 벡터를 학습(조정)하는것이 목표입니다.

> Perceptron

**손으로 weight를 조정해서 and나 or 문제를 풀어보면 이해에 도움이 많이 됩니다!**

<center><img src="https://user-images.githubusercontent.com/31475037/59171562-651c3b80-8b7e-11e9-9ede-2b6bfd8e67e6.png" width="100%"></center>
이를 통해 Perceptron은 and나 or 문제를 풀 수 있습니다.



이러한 Perceptron은 뉴런이 다음 뉴런으로 신호를 보내는 과정을 모방하여 만든 것입니다.

다음 그림과 같이 크게 이전 뉴런으로부터 신호를 받는 cell body 부분과 다음 뉴런으로 신호를 전달하는 axon 부분이 있습니다. 

이 구조를 모방하여, 입력값을 받아 cell body 부분에서 weight와 input이 곱해지고 이를 sigmoid(활성화) 함수를 이용해 다음 뉴런으로 보내줍니다.

> 뉴런 구조를 따와서 만듬

![](https://user-images.githubusercontent.com/31475037/59965034-06729c80-9544-11e9-8151-c38ac8825d83.png)

안타깝게도 아무리 weight를 조정해봐도 XOR 문제를 풀 수가 없습니다.


> 그렇다면 XOR문제를 Perceptron이 해결 가능할까요?


<center><img src="https://user-images.githubusercontent.com/31475037/59157949-5dad5180-8aee-11e9-861f-1b07de6e436d.png" width="90%"></center>
이런 문제를 해결하기 위해 제안된 것이 바로 MLP입니다.

### MLP(Multi Layer Perceptron)

그렇게 나온 모델이 바로 MLP입니다.

MLP는 input layer와 output layer 사이에 하나 이상의 hidden layer를 추가하여 학습하는 모델입니다.

MLP 모델은 기존의 단일 Perceptron이 해결하지 못한 XOR 문제를 성공적으로 풀었습니다.

> XOR 문제를 풀 수 있음

<center><img src="https://user-images.githubusercontent.com/31475037/59157950-5dad5180-8aee-11e9-8dde-bf5e6d623d37.png" width="90%"></center>
MLP 구조에서 hidden layer를 깊게(많이) 쌓은 네트워크 모델이 바로 우리가 일반적으로 많이 알고 있는 Deep Neural Network 입니다.

아래 그림의 hidden layer에서 처럼 다음 layer의 모든 뉴런과 다 연결 되있는 layer를 fully cunnected layer라고 부릅니다.

일반적으로 input layer는 layer 개수를 셀 때 포함시키지 않습니다.

> 일반적인 MLP 구조



<center><img src="https://user-images.githubusercontent.com/31475037/59965089-b47e4680-9544-11e9-9703-756f382ef459.png" width="90%"></center>
## Universal Approximation Theorem(UAT)

Neural Network는  Universal Approximation(만능 근사)를 할 수 있습니다. 우리가 뉴럴 네트워크를 인공지능의 두뇌로 사용할 수 있는 것은 어떤 종류의 입력이든지 원하는 출력을 내놓도록 훈련시킬 수 있다는 믿음이 있기 때문입니다. 이처럼 Neural Netork의 weight를 조절하면 우리가 원하는 함수를 근사하여 출력 시킬 수 있다는 이론입니다.

아래 링크는 UAT를 시각적으로 체험할 수 있는 페이지입니다.

[Universal Approximation](http://neuralnetworksanddeeplearning.com/chap4.html)

<br>

**읽어볼만한 글**

[Perceptron](https://www.youtube.com/watch?v=xMRKQBbHOzA&list=PL6ip5tgLI7PcStXTz8CRMhNWmT8M0dAWO&index=2)

[Neural Net의 역사](http://solarisailab.com/archives/1206)

[Neural Net의 역사2](https://jinseob2kim.github.io/deep_learning.html)

[Neural Net pytorch 구현 코드](https://github.com/chacha95/Deeplearning_example/blob/master/04.Neural_Net.ipynb)

[Deep learning](http://sanghyukchun.github.io/54/)

[Deep learning 소개](https://drive.google.com/file/d/17QKVd3OC4-A9UuvfMcFpQp3ITPSLiy6V/view?fbclid=IwAR0mxgxrJqSUaD3DFKLY4owgdKYfCqZw5vgLs8LKKjCF_tfsi5j7ZhZBxI8)

**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

<br>