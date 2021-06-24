---
layout: post
title: RNN(Recurrent Neural Network)
tags: [ML]
use_math: true
---

# RNN

RNN은 hidden node가 방향을 가진 엣지로 연결되어 순환구조를 이루는 Neural Net의 한 종류입니다.

음성, 문자, 비디오와 같이 sequantial한 데이터처리에 적합한 모델입니다. 

녹색 박스는 hidden state, 빨간 박스는 input x, 파란 박스는 output y입니다.

hidden state는 직전 시점의 hidden state를 받아 업데이트 됩니다.

> Vanila RNN



![](http://i.imgur.com/s8nYcww.png)



**몇가지 예시(아래 그림의 순서대로 입니다.)**

- one to one

  RNN이 아닌 경우로 input으로 fixed-size 벡터, output으로 fixed-size 벡터 입니다.

  e.g. image classification

- one to many

  input으로 fixed-size 벡터, output으로 sequence output입니다.

  e.g. image captioning

- many to one

  input으로 sequence 벡터가 들어옵니다.

  e.g. sentiment analysis(감정 인식)

- many to many(case 1)

  input으로 sequence 벡터가 들어오고, output으로 sequence 벡터가 나옵니다.

  e.g. Mahcine Translation(번역)

- many to many(case 2)

  sequence input과 sequence output의 synce를 맞춰줍니다

  e.g. video captioning

![](https://miro.medium.com/max/700/1*XosBFfduA1cZB340SSL1hg.png)



**"hello" 예측 - character level language model**

charactoer-level language model로 RNN을 train해 볼까요?

이 말은 즉, RNN에게 text 덩어리는 줍니다. 그리고 RNN이 주어진 이전의 문자열에 대해 다음 문자를 예측 하는 역할을 합니다.

1. “e”의 확률값은 주어진 context(문맥) “h”와 비슷해야 한다.
2. “l”의 확률값은 주어진 context “he”와 비슷해야 한다.
3. “l”  확률값은 주어진 context “hel”와 비슷해야 한다.
4. “o”  확률값은 주어진 context “hell”와 비슷해야 한다.

각 character를 1-of-k encoding 방식을 이용해 4차원 벡터의 형태로 encoding합니다.

- "h" : [1, 0, 0, 0]
- "e" : [0, 1, 0, 0]
- "l" : [0, 0, 1, 0]
- "l" : [0, 0, 0, 1]

hidden layer는 3개의 뉴런으로 이뤄져 있다고 가정해봅시다.

우리의 목표는 RNN에 "h"의 1-of-k encoding된 벡터 [1, 0, 0, 0]을 입력했을 때, hidden laeyr를 거쳐 나온 output  layer에서의 값이 초록색으로 표시된 값이 제일 크게 만드는 것이 목표입니다.

<center><img src="https://miro.medium.com/max/700/1*bdR_utmqOoKu_h-JyeoXYA.png"></center>
그렇다면 RNN의 예측값이 정답과 같도록 만드려면 어떻게 해야 할까요?

바로 backpropagation을 통해 weight를 조절해 나가면 됩니다.

이처럼 RNN은 weight를 share하는 구조를 가졌으며, sequential한 정보를 처리하는데 최적화 되있습니다.

([backpropagation 포스트](https://chacha95.github.io/2018-11-17-Deeplearning1.5/))

<br>

## LSTM(Long Shor Term Memory)

RNN은 관련 정보와 그 정보를 사용하는 지점 사이 거리가 멀 경우 backpropagation 시 gradient vainishing 문제가 생겨 학습이 잘 되지 않습니다.

이 문제를 극복하기 위해 고안된 것이 LSTM이며 이는 RNN의 hidden state에 cell-state를 추가한 구조 입니다.(long-term dependency problem을 극복하기 위해 제안됨)

### LSTM과 RNN의 구조적 차이

diagram으로 표현된 각각의 구조는 다음과 같습니다.

![](http://i.imgur.com/jKodJ1u.png)

- pink circle : vector addition, element-wise multiplication 등등
- vector transfer : vector의 flow 표현

![](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM2-notation.png)

그렇다면 LSTM 구조에 대해 자세히 알아볼까요?

### The Core Idea of LSTM

LSTM의 core idea는 cell state입니다.

The cell state는 conveyer belt와 같습니다. 전체 chain을 straight하게 지나가기 때문입니다. 바뀌지 않은 채로 그냥 흐르기만 하기에 정보(이전 hidden state에서 온 정보가 중추가 됨)를 전달하기가 쉽습니다.

> conveyer belt 처럼 지나가는 cell state

<center><img src="http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-C-line.png"></center>
LSTM은 cell state에 gate 구조를 이용해 조심스럽게 조절해가면서 정보를 추가하거나 제거해 나갑니다.

gate들은 neural net layer의 결과물에 pointwise multiplication을 통해 나온 결과값을 통해 cell state 영향을 미칩니다.

sigmoid를 통해 나온 결과물은 [0, 1]범위이고, 그 값이 0이면 cell state에 추가할 정보가 없다는 것이고, 1이면 cell state에 모든 정보를 추가하라는 의미입니다.

> gate 구조

<center><img src="https://user-images.githubusercontent.com/31475037/60249911-ac395900-9900-11e9-85d5-4b78831dd032.png" width="40%"></center>
### Step into LSTM

LSTM으로의 첫번째 step은 바로 cell state에서 어떤 정보를 버릴지 결정하는 것입니다.

이 결정은 "forget gate layer"라 불리는 sigmoid layer에 의해서 결정됩니다.

이전 hidden state를 통해 나온 h 벡터와 새로운 입력값 x 벡터를 concat 한 뒤, forget gate에 넣어줍니다.

forget gate를 통해 나온 값이 1이면 cell state에 추가되게 됩니다. 반면에 0이면 cell state에 추가하지 않습니다.

즉, 이 단계는 step은 이전 hidden state에서 온 contex와 입력 벡터의 연관성을 구한 뒤, forget gate를 통해 cell state에 이 새로운 정보를 추가할지 안할지 정하는 step 입니다.

> forget gate

![](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-focus-f.png)



다음 step으로는 새로운 정보를 cell state에 추가하는 step 입니다.

이 step은 두 단계로 이뤄져 있으며, 첫번째는, "input gate layer"가 우리가 어떤 value를 업데이트 할지 결정합니다. 다음으로 tanh(하이퍼볼릭 탄젠트) layer가 새로운 candidate value C를 만들어 냅니다. 

여기서 i가 candidate value C에 대해 scale factor 역할을 해줍니다.

> input gate

![](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-focus-i.png)

이제 old cell state를 new cell state로 업데이트할 차례입니다.

forget gate와 input gate를 통해 업데이트 된 내용에 대해 cell state에 새로운 정보들을 업데이트 해줍니다.

> new information update

![](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-focus-C.png)



마지막으로 우리는 무엇을 ouptut으로 낼지 결정해야합니다. 이 결정은 현재 cell state를 바탕으로 결정됩니다. 먼저 sigmoid에 이전 hidden state를 넣고 이를 현재 cell state와 곱해준 값을 output으로 출력합니다.

![](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-focus-o.png)



<br>

**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

[RNN](https://www.youtube.com/watch?v=UNmqTiOnRfg&t=790s)

[RNN2](https://www.youtube.com/watch?v=PahF2hZM6cs)

[LSTM](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)

**읽어볼만한 글**

[RNN](https://towardsdatascience.com/animated-rnn-lstm-and-gru-ef124d06cf45)

[RNN2](https://towardsdatascience.com/illustrated-guide-to-lstms-and-gru-s-a-step-by-step-explanation-44e9eb85bf21)



<br>

