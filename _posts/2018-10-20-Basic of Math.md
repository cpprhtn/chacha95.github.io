---
layout: post
title: Basic of Math
tags: [Math]
use_math: true
---

### Scalars, Vectors, Matrices and Tensors

**Scalars**

single number, 소문자 이탤릭체로 표현 합니다.

> 실스 스칼라 변수 s

$$
s \in \mathbb{R}
$$



**Vectors**

숫자들의 array, 소문자 볼드체로 표현 합니다.

벡터의 인자들은 문자 아래첨자 인덱스를 이용해 표현 합니다.

> 표현

$$
\textbf{v} = \begin{bmatrix}
v_1\\
v_2\\
\vdots\\
v_n
\end{bmatrix}
$$



**Matrices**

행렬은 숫자들의 2D array, 대문자 볼드체로 표현 합니다.

> m행 n열을 갖는 행렬의 표현

$$
\textbf{A} \in \mathbb{R}^{m \times n}
$$

> 인자를 표현

$$
A_{m,n}
$$

> 표현

$$
\textbf{A} = \begin{bmatrix}
A_{1,1} \quad A_{1,2}\\
A_{2,1} \quad A_{2,2}
\end{bmatrix}
$$



**Tensors**

grid에 정렬되어 있는 숫자들의 array, 행렬보다 높은 차원의 인덱스를 가진 array 입니다.

Vector, Matrix, Cube를 일반화한 경우이다. 즉 1차원 Tensor가 Vector이고 2차원 Tensor가 Matrix이고 3차원 Tensor가 Cube입니다.

> Vector, Matrix, Cube ... Tensor의 개념

<center><img src="https://user-images.githubusercontent.com/31475037/59034498-1df42900-88a6-11e9-9999-4c3b9035ff68.png" width="600" Height="400"></center>
<br>

### Multiplying Matrices and Vectors

두 행렬을 곱하는 matrix product 행렬곱에서  A와 B를 곱할 때, A의 열과 B의 행 수가 같아야합니다.

> 행렬곱

<center><img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/9ea18d2fd6c39f5522e50b201b700a32ef43438a"></center>
두 행렬을 곱할 때, 행렬곱이 아닌 인자들간의 곱은 element-wise product 또는 Hadamard product 라 부릅니다.

> element-wise product

$$
\textbf A \odot \textbf B
$$



**LU decomposition**

행렬을 Lower Trianlge과 Upper Trianlge 두개의 곱으로 분해하는 방식입니다.

보통 가우스 소거법(연립 일차방정식을 풀이하는 방법)에서 많이 씁니다.

> LU decomposition

<center><img src="https://media.geeksforgeeks.org/wp-content/uploads/matrix2-1.png"></center>
<br>

### Integral(적분)

**Integration by parts(부분 적분)**

미분 가능한 연속함수에 대해 다음과 같이 표현 가능합니다.
$$
\int u(x)v^\prime (x)dx = u(x)v(x) - \int u^\prime(x) v(x)dx
$$



**Integral log(x)**
$$
\int log(x)dx = xlog(x) - \int x \frac {d}{dx}log(x)dx 
$$

**Integral 1/x**
$$
\int \frac {1} {x} = ln \left| x \right| + C
$$



<br>

### Derivative(미분)

미분은 이런 **특정순간**의 변화량을 의미합니다. X의 **작은 변화**가 함수 f(x)를 얼마나 변화 시키냐는 의미입니다.

즉, 함수에서 미분의 개념이 기울기라는 것을 의미합니다.

> 미분의 정의

<center><img src="https://user-images.githubusercontent.com/31475037/64474449-15b2cf00-d1b0-11e9-826c-04311109bcd4.PNG" width="40%"></center>
**Partial Derivative(편미분)**

각 variable(변수)에 대한 미분값입니다.

 <center><img src="https://user-images.githubusercontent.com/31475037/64474450-15b2cf00-d1b0-11e9-9882-3585f4764005.PNG" width="80%"></center>
**Gradient**

Multi-variable 일 경우의 미분 계산 방식입니다.

> gradient 표현

<center><img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/832275c31fbf4723a0ce1df6ff0611bf264ab8eb"></center>
> gradient  예시

$$
f(x,y) = x^2 + y^2일 \ 때
$$

$$
\nabla f = (\frac {\partial f} {\partial x}, \frac {\partial f} {\partial y}) = (2x, 2y)
$$



**Jacobbian**

Multi-variable 일 경우의 gradient의 일반화입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/64474451-164b6580-d1b0-11e9-95a7-8ff4d6d8f5af.PNG" width="50%"></center>
**Hessian**

1차 미분이 아닌 2차 미분일 경우입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/64474452-164b6580-d1b0-11e9-8b53-e2865c311b23.PNG" width="80%"></center>
<br>

### Norms

벡터의 크기(Magnitude, Scale)를 표현하기 위한 함수입니다.

Vector x의 norm은 원점으로부터 point x까지의 거리라고 볼 수 있습니다.



**L1 Norm**

L1 Norm입니다.
$$
\lVert x \rVert_1 \quad
$$


**L2 Norm**

원점으로부터 벡터 포인트까지의 Euclidean distance를 나타내기에 Euclidean norm으로도 불린다.

밑첨자 2를 생략하기도 합니다.

> 표현

$$
\lVert x \rVert_2 \quad or \quad \lVert x \rVert
$$

Squared L2 Norm도 자주 쓰입니다. 왜냐하면 루트 안에 들어가 있는 경우보다 꺼내져 있을 때 연산하기 편리하기 때문입니다. 예를들어 squared L2 Norm은 미분시 각각의 인자에 대해 미분 가능하지만, L2 Norm은 전체에 대해 미분해야 합니다.

> squared L2 Norm

$$
x^\top x
$$



**Max Norm**

벡터에서 가장 큰 절대값을 norm으로 구합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/64488447-3d269c00-d283-11e9-8d81-afd74e90bf4c.PNG" width="30%"></center>
**Frobenius Norm**

벡터의 크기가 아닌 행렬의 크기를 구합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/64488448-3d269c00-d283-11e9-974b-b16736b134dd.PNG" width="30%"></center>
**Dot Product**

두 벡터의 dot product도 norm으로 표현 가능합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/64488449-3d269c00-d283-11e9-86a3-3d990105e8b6.PNG" width="40%"></center>
<br>

### Product

**Inner Product**
$$
a \cdot b = a^\top b = <a, b>
$$





**Outer product**
$$
a \otimes b = ab^ \top
$$





**Orthogonal Vectors**

Innter product시 값이 0이 나와야합니다.

벡터 x와 y가 직교한다는 의미입니다.
$$
x \cdot y = 0, \quad(x\perp y)
$$



**Orthogonal Matrix**
$$
AA^T = E, \quad A^{-1} = A^T
$$



**Orthonormal**

orthonormal 하기 위해선 다음 2가지 조건이 만족해야합니다.

1. 내적이 0

$$
x\cdot y = 0
$$

2. 유닛 벡터(unit vector)

   크기가 1인 벡터

$$
\Vert x \Vert = 1, \ \Vert y \Vert = 1
$$

<br>

## Linear Algebra

**Linear하다는 의미**

중첩의 원리(superposition principal)가 적용되는 함수는 선형 함수라 부름 아닐시 비선형 함수라 부릅니다.

선형 모델의 경우 파라미터 추정/모델 해석 등이 쉬움 그러나 복잡한 패턴의 데이터 분석이 힘듭니다. 하지만 비선형 모델의 경우 파라미터 추정/모델 해석 등이 어렵지만 복잡한 패턴의 데이터 분석이 가능합니다.

그래서 비선형 모델을 선형 모델로 근사 시켜 어려운 문제를 쉽게 만들어 풉니다.



**Linearly Independence(선형 독립)**

set of vectors 중 하나를 다른 것의 선형 조합으로 정의 할 수없는 경우 set of vectors는 선형 독립이라고합니다.

> Linearly dependence(선형 종속)의 조건

<center><img src="https://user-images.githubusercontent.com/31475037/64503161-b87c6200-d304-11e9-8981-2abbd398425d.PNG"></center>
Linearly dependence가 아닐 경우 Linearly independence합니다.



**Span(생성)**

벡터들의 연산으로 생성되는 subspace를 말합니다.

e.g. 벡터 u와 벡터 v는 평면을 span 합니다.

> 오른쪽의 파란색 벡터 w는 벡터 u와 벡터 v가 span하는 공간 안에 있지 않다.

<center><img src="http://i.imgur.com/Ss2LDRI.png"></center>
**Basis**

어떤 공간을 **Span**하면서 그들이 **Independent**한 벡터를 말합니다.

e.g. 일반적인 2차원 공간의 basis는 (1,0), (0,1) 두 벡터 입니다. 두 벡터에 의해 subsapce가 span 됩니다.



**Linear Algebra에서의 행렬의 의미**

행렬을 이용해 선형 변환을 표현합니다.

자세한 내용은 [공돌이의 정리 노트](https://angeloyeo.github.io/2019/07/15/Matrix_as_Linear_Transformation.html)에 자세히 설명 되어 있습니다.

행렬은 벡터를 변환 시켜 다른 벡터를 출력해줍니다.

> 행렬은 벡터를 변환시켜주는 연산자

<center><img src="https://raw.githubusercontent.com/angeloyeo/angeloyeo.github.io/master/pics/eigen_vector_values/pic1.png"></center>
<br>

### Eigen

**Eigenvector** : "벡터 x에 선형변환 A를 했을 때, 그 크기만 변하고 원래 벡터와 평행한 벡터 x는 무엇인가요?"

**Eigenvalue** : "그렇다면, 그 크기는 얼마만큼 변하였나요?"

아래 두 장의 그림은 원래 이미지가 옆으로 기울어진 모양으로 변하는 선형변환을 보여주고 있습니다. 이 선형 변환에서 수평 축은 그대로 수평 축으로 남기 때문에 푸른색 화살표는 방향이 변하지 않지만 붉은색 화살표는 방향이 변하게 됩니다. 따라서 푸른색 화살표만 이 선형변환의 **eigenvector**가 됩니다. 또한 푸른색 화살표의 크기가 변하지 않았으므로 이 벡터의 **eigenvalue**는 1입니다.

Eigenvector의 크기(scale)가 eigenvalue의 값입니다. 

> 기하학적 의미

<center><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/Mona_Lisa_eigenvector_grid.png/405px-Mona_Lisa_eigenvector_grid.png"></center>
> Eigenvalue&eigenvalue 식

$$
\textbf A \textbf v = \lambda \textbf v \quad (\textbf v:eigenvector,\ \lambda:eigenvalue)
$$

Eigenvector를 구하기 위한 필요 충분 조건은 다음과 같습니다.

> Eigenvector 구하는 법

$$
det(\textbf A - \lambda \textbf I) = 0
$$



**Eigen Decomposition**

이번 포스트에서 소개하는 eigen decomposition 방법은 **실수**로만 분해되는 경우만 해당됩니다.

소인수 분해로 숫자를 쪼개어 각 숫자의 성질을 분석하듯이, 행렬 또한 쪼개어 봄으로써 행렬의 성질을 파악하고 이용할 수 있습니다. 이 중 가장 흔히 사용되는 방법이 eigen decomposition입니다.

행렬을 eigenvector와 eigenvalue로 분해하는 방법입니다.



> 행렬 V

$$
\textbf V = [\textbf v_1, \textbf v_2 \cdots \textbf v_n]
$$



> diagonalized matrix **λ**

$$
diag(\lambda) = \begin{bmatrix}
\lambda_1 & 0 & \cdots & 0 \\
0 & \lambda_2 & \cdots &0 \\
\vdots && \ddots \\
0 & 0 & \cdots & \lambda_n
\end{bmatrix}
$$

> Eigen decomposition

$$
\textbf A = \textbf V diag(\lambda)\textbf V^{-1}
$$



Eigendocomposition을 이용해 정방행렬을 diagonalization(대각화) 할 수 있습니다. 이를 통해 전체 연산량을 줄일 수 있습니다.(행렬 **λ**는 sparse하기에 계산량이 현저히 적음)

Eigendecomposition을 하기 위해서는 linearly independent 해야합니다. 

즉 n차 정방행렬이 eigendecomposition이 가능하려면 n개의 basis가 존재해야 합니다.

Eigen decomposition 한 뒤 다음과 같이 sparse한 matrix에 대해서 표현할 수 있습니다.

> Power of A

$$
\textbf A^k = \textbf V \lambda^k \textbf V^{-1}
$$



**SVD(Singular Value Decomposition)**

Eigendecomposition을 mxn 행렬로 일반화한 경우입니다. 즉, singlular vectors와 singular values로 분해합니다.

Eigen decomposition은 정방행렬만 분해가 가능하지만, SVD는 정방행렬이 아니더라도 가능합니다.

> SVD

$$
\textbf A = \textbf U \textbf D \textbf V^T
$$

**A**가 m x n 행렬이라 했을 때, **U**는 m x m 행렬이고, **D**는 m x n 행렬, **V**는 n x n 행렬입니다.

**U**와 **V**는 orthogonal 행렬로 정의됩니다. **D**는 diagonal 행렬로 정의됩니다.

> **U**

$$
\textbf U = \textbf A \textbf A^T
$$



> **V**

$$
\textbf V = \textbf A^T \textbf A
$$



> **A**를 SVD한 결과(중간에 시그마가  **D** 행렬임)

<center><img src="https://raw.githubusercontent.com/angeloyeo/angeloyeo.github.io/master/pics/2019-08-01_SVD/pic_SVD.png"></center>
<br>

### The Determinant(행렬식)

행렬을 입력으로 받아 실수 스칼라값을 출력하는 일종의 함수입니다.

Determinant는 모든 eigenvalue의 곱과 같습니다.

Determinant의 절댓값은 행렬이 공간을 얼마나 늘리고 축소시키는지에 대한 지표가 될  수 있습니다.



**Properties of det**

> Identity

$$
det(\textbf I) = 1
$$

> Transpose

$$
det(\textbf A ^T) = det(A)
$$



> Inverse

$$
det(\textbf A^{-1}) = \frac {1} {det(\textbf A)}
$$

> Multiplication

$$
det(\textbf A \textbf B) = det(\textbf A)det(\textbf B)
$$



<br>



**참고 강의**

**[Deeplearning Book](https://www.deeplearningbook.org/)**

[공돌이의 수학정리 노트](https://angeloyeo.github.io/2019/08/06/determinant.html)

[ratsgo's blog](https://ratsgo.github.io/linear%20algebra/2017/03/24/Ldependence/)