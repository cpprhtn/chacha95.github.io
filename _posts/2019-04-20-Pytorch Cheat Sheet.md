---
layout: post
title: Pytorch Cheat Sheet
tags: [Python]
---

Pytorch 코딩 시 유용했던 코드 모음입니다.

shape 표현시 ( ) e.g. (2, 4)

element 표현시 [ ] e.g. [[2, 3], [4, 5]]

식으로 통일해서 표현합니다.

### Numpy

> Basic

```python
a = [1, 2, 3]			# python list 선언
a = np.array(a)			# list to numpy array
print(type(a)) 			# <class 'numpy.ndarray'>
print(a.shape)			# (3,) array의 shape를 출력
print(a.dtype)			# dtype('int32')
a = a.astype('uint8')	# uint8 자료형으로 형변환
```



> np.reshape()

```python
a = np.array([1, 2, 3, 4])	# shape = (4,)
a = np.reshape(a, (2, 2))	# shape = (2, 2)
a = np.reshape(a, (2, -1))	# shape = (2, 2), -1을 쓰면 알아서 shape 맞춰줌
```



> np.squeeze()

```python
a = np.array([[[0], [1], [2]]])		# shape = (1, 3, 1)
np.squeeze(a).shape					# shape = (3,)
```



> np.stack()

```python
a = np.array([1, 2, 3])
b = np.array([2, 3, 4])

# axis=0은 행 단위로 붙여줌
# axis=1은 열 단위로 붙여줌
np.stack((a, b), axis=0)	# array([[1, 2, 3],
							#		 [2, 3, 4])

np.stack((a, b), axis=1)	# array([1, 2],
							#		[2, 3],
    						# 		[3, 4])
```



> np.concatenate()

```python
a = np.array([1, 2, 3])
b = np.array([2, 3, 4])

np.concatenate((a,b), axis=0)	# [1, 2, 3, 2, 3, 4]
np.concatenate((a,b), axis=1)	# error

a = np.array([[1, 2, 3], 
              [4, 5, 6]])
b = np.array([[7, 8, 9], 
              [10, 11, 12]])

np.concatenate((a,b), axis=0)	# [[1, 2, 3],
								#  [4, 5, 6],
    							#  [7, 8, 9],
        						#  [10, 11, 12]]
            
np.concatenate((a,b), axis=1)	# [[1, 2, 3, 7, 8, 9],
								#  [4, 5, 6, 10, 11, 12]]

```

np.stack()과는 적용 방식이 좀 다르니 사용시 주의!



> list로 값 받은 후 numpy array로의 변환

```python
list = []
for i in range(10):
    list.append(i)
arr = np.array(list)
```



<br>

### OpenCV

> BGR to RGB

```python
cv2.cvtColor(img, cv2.COLOR_BGR2RGB) # cv2는 BGR로 읽기에 RGB로 변환 필수
```



> cv2.resize()

```python
img_size = (112, 112)
cv2.resize(img, img_size)	# vid_size만큼 리사이즈 해줌
```



<br>

### Pytorch

> torch.ones()

```python
torch.ones(size)
torch.ones(2,3)		# -> shape (2,3)이고 1로 가득찬 tensor 생성
```



> tensor의 shape, type

```python
x = torch.zeros(2, 4)

x.shape		# (2, 4)
x.size()	# (2, 4)
			# 정확히는 torch.Size([2, 4])의 형태로 출력됩니다.

x.type()	# torch.LongTensor
			# 데이터의 자료형 출력
```



> torch.squeeze()

```python
x = torch.zeros(2, 1, 2, 1, 2)
torch.squeeze(x)				# (2, 2, 2)
```



> torch.from_numpy()

```python
list_arr = [1, 2, 3]					# python list array
np_arr = np.array(list_arr)				# list -> numpy array
tensor_arr = torch.from_numpy(np_arr)	# numpy -> tensor array
np_arr2 = np.array(tensor_arr)			# tensor -> numpy array
```



> tensor data type casting

```python
arr = np.array([1, 2])
arr = torch.from_numpy(arr)
arr = arr.type(torch.float32)	# float32 casting
arr = arr.float()				# float32 casting
```

[Tensor Data Type](https://pytorch.org/docs/stable/tensors.html)



> nn.Softmax

```python
# default numpy array
arr = np.array([[1, 2, 3, 1, 2, 1, 1, 1],
                [1, 2, 3, 2, 1, 2, 3, 1]])

# tensor로 바꿀시 torch.LongTensor로 바뀜
arr = torch.from_numpy(arr)
# Softmax에 사용하기 위해선 float32로 바꿔야함
arr = arr.type(torch.float32)

m = nn.Softmax(dim=1)
output = m(arr)
output = torch.sum(output, dim=1)
print(output)						# [1.0000, 1.0000]

```



> nn.CrossEntropy

[pytorch에서의 CrossEntorpy](https://github.com/rasbt/stat479-deep-learning-ss19/blob/master/L08_logistic/code/cross-entropy-pytorch.ipynb) 구현 방식





> torch.cat()

```python
# np.concatenate()와 똑같이 작동한다
a = np.array([[1, 2, 3], 
              [4, 5, 6]])
b = np.array([[7, 8, 9], 
              [10, 11, 12]])

# numpy array에서 tensor로
x = torch.from_numpy(a)
y = torch.from_numpy(b)

torch.cat((x, y), dim=0)	# [[1, 2, 3],
							#  [4, 5, 6],
    						#  [7, 8, 9],
        					#  [10, 11, 12]]
            					
torch.cat((x, y), dim=1)	# [[1, 2, 3, 7, 8, 9],
							#  [4, 5, 6, 10, 11, 12]]
```



> torch.split()

```python
a = np.array([[1, 2, 3], 
              [4, 5, 6]])

# numpy array에서 tensor로
x = torch.from_numpy(a)

# split_size_or_sections : 각 chunk의 size 지정
torch.split(x, split_size_or_sections=2, dim=0) # [[1, 2, 3],
												#  [4, 5, 6]]
torch.split(x, split_size_or_sections=2, dim=1)	# [[1, 2],
												#  [4, 5],
    											#  [3, 6]]

# e.g.
x1, x2 = torch,split(x, 2, 0)
```



> torch.chunk()

```python
a = np.array([[1, 2, 3], 
              [4, 5, 6]])

# numpy array에서 tensor로
x = torch.from_numpy(a)

# chunks : nuber of chunks to return -> 몇개로 나눌것인가?
torch.chunk(x, chunks=2, dim=0)	# [[1, 2, 3],
								#  [4, 5, 6]]
torch.chunk(x, chunks=2, dim=1)	# [[1, 2],
								#  [4, 5],
    							#  [3, 6]]
        
# e.g.
x1, x2 = torch.chunk(x, 2, dim=2)
```



> .permute()

pytorch는 모델에 들어가는 tesnor의 shape가 [N, C, H, W]임

torchvision.transforms의 ToTensor()에서 자동으로 바꿔주지만, OpenCV 사용시 fromarray()함수와 잘 조합해 바꿔줘야함

.permute()를 이용해 바꿔줌



> .view()

```python
# resahpe 기능 수행
x = torch.randn(4, 4)	# (4, 4)
z = x.view(-1, 8)		# (2, 8)
```



> .eval()

.eval함수를 모델에 적용해 test 모드로 바꿔줘야함(Batch Norm 때문에)



> 멀티 GPU 사용 code

```python
 if isinstance(input.data, torch.cuda.FloatTensor) and self.ngpu > 1:
	output = nn.parallel.data_parallel(self.main, input, range(self.ngpu))
```



> torchvision.transforms

```python
transforms.Compose([
      transforms.CenterCrop(10),
      transforms.ToTensor( ),
])
```

여러 transform들을 묶습니다.



> Contigious and non-contigious tensor

[Pytorch Forum](https://discuss.pytorch.org/t/contigious-vs-non-contigious-tensor/30107)



> Tensor.reshape and Tensor.view()

[Pytorch Forum](https://discuss.pytorch.org/t/in-pytorch-0-4-is-it-recommended-to-use-reshape-than-view-when-it-is-possible/17034)

Tensor.view()는 tensor가 메모리에 저장되지 않으나 Tensor.reshape는 저장됨

이는 Contigious와 non-contigious의 차이

> cudnn.benchmark

[Pytorch Forum](https://discuss.pytorch.org/t/what-does-torch-backends-cudnn-benchmark-do/5936/2)

cudnn.benchmark 옵션을 사용하게 되면 입력사이즈가 일정할때 cudnn이 최적의 알고리즘을 찾아줌



> pytorch gpu control

[towardDataScientiest](https://towardsdatascience.com/speed-up-your-algorithms-part-1-pytorch-56d8a4ae7051) 글에서 gpu의 사용과 할당에 대해 자세히 설명되어 있다.

```python
# default device id는 0이지만 바꿀수 있음
torch.cuda.set_device(2)
# 쓰고 싶은 gpu의 id를 설정해줘야 쓸 수 있음
import os
os.environ["CUDA_VISIBLE_DEVICES"] = "0,2"

# gpu device를 설정해줌
cuda0 = torch.device('cuda:0')
cuda1 = torch.device('cuda:1')

# gpu에 tesnor를 할당하는 방식 3가지
x = torch.Tensor([1., 2.], device=cuda1)
# Or
x = torch.Tensor([1., 2.]).to(cuda1)
# Or
x = torch.Tensor([1., 2.]).cuda(cuda1)
```



<br>

### Python

> _ (언더 스코어)

파이썬에서 언더 스코어는 다음과 같은 역활을 합니다.

1. 마지막으로 실행된 결과값이 _에 저장됩니다.

2. 특정값을 무시합니다. 

   e.g. x, _, y = (1, 2, 3) --> x=1, y=3

3. 매직 메소드

   클래스안에 정의할 수 있는 스페셜 메소드, 클래스를 built-in-type과 같은 작동을 하게 함

   던더, 더블언더 스코어로 불림



> 매직 메소드

```python
class Dog(object):
    def __init__(self, name):
        print('이름 {0}').format(name)

dog_1 = Dog('pink')
```

매직 메소드의 대표격으로는 init이 있으며 클래스의 인스턴스를 생성시 자동으로 불려져 클래스를 초기화해줍니다.



```python
class foo:
    def __init__(self, a, b, c):
        pass
    
    def __call__(self, a, b, c):
        print(a + b + c)
   
x = foo(1, 2, 3)		# foo class 인스턴스 생성
print(x(1, 2, 3))		# __call__ 함수 불려짐
```

매직메소드 call을 이용해 클래스명을 함수처럼 쓸 수 있어 문법이 간결해집니다.



> isinstance( )

```python
isinstance(object, class_info)

isinstance('hello', str) -> True 반환
isinstance(1, str) -> False 반환

```

object가 class_info의 인스턴스인지 점검해 True/False 값을 리턴합니다.

object의 데이터 타입을 확인 할 수 있음



> assert

```python
assert condition

a = 3
assert a == 2 -> AssertError 발생
```

condition 조건에 맞지 않으면 AssertError를 발생



> main함수

```python
if __name__ == "__main__"
```

파이썬의 main 스크립트에는 위와 같은 조건을 걸어주는데 파이썬은 자동으로 실행되는 메인함수가 없습니다.

name은 현재 모듈의 이름을 담고 있는 내장 변수고, main이라 함은 해당 스크립트를 직접 실행 하였느냐, 아니면 다른 스크립트에서 불렀느냐의 차이입니다.



> 포매팅

```python
"%d 살입니다." %20 					  # 20살입니다.
"{0} {1}".format('python', 'script') # python script
```



> 폴더 내 파일 읽기

```python
for file in sorted(listdir(base_dir))	# sorted를 해줘야 정렬됨

file.split('_')		# file 명을 특정 인자로 split함
					# 결과값은 list로 반환 -> 인덱싱해서 사용
```



> list comprehension

```python
# 반복문
evens = [x*2 for x in range(11)]						# 1, 4, 9
# 반복문 + 조건문
non_squars = [x for x in range(101) if sqrt(x)**2 != x]	# 2, 3, 5, ....
```

pythonic한 반복문, 조건문 방식



> *args

파라미터를 몇개 받을 지 모를 때 사용

![](https://user-images.githubusercontent.com/31475037/62348024-b28da700-b536-11e9-9020-8b2c162d591a.PNG)



> **kargs

파라미터 명을 보낼 수 있으며, 이를 딕셔너리 형태로 저장

![](https://user-images.githubusercontent.com/31475037/62348057-cafdc180-b536-11e9-97d1-9fad70402405.PNG)



> argparse

```python
parser.add_argment('--cond1',
                  type=int,
                  required=True,
                  default=4,
                  help="설명을 입력")
```





<br>

**참고 자료**

[Pytorch Doc](https://pytorch.org/tutorials/beginner/ptcheat.html)

[매직 메소드](http://schoolofweb.net/blog/posts/%ED%8C%8C%EC%9D%B4%EC%8D%AC-oop-part-6-%EB%A7%A4%EC%A7%81-%EB%A9%94%EC%86%8C%EB%93%9C-magic-method/)

[CS231N tutorial](http://cs231n.github.io/python-numpy-tutorial/)

<br>