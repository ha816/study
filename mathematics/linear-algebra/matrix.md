# Matrix(행렬)

앞서 벡터라는 대상을 알았으니 다음은 벡터 대상간의 관계를 나타내보자. 행렬은 수를 사각 형태로 나열한 것이다. 

$$\begin{pmatrix}
2&0\\
0&3\\
\end{pmatrix}$$

행렬이므로 `행` 과 `열`의 순으로 표기한다. 일반적으로 행렬은 알파벳 대문자, 행렬의 성분은 소문자로 쓴다. 

## 행렬의 곱하기

고기 $x$그램, 콩 $y$그램, 쌀 $z$그램을 샀다고 하자 총 비용은 얼마일까 그리고 총 몇 칼로리 일까?

$$\begin{pmatrix}
y_{money}\\
y_{calory}\\
\end{pmatrix} =
\begin{pmatrix}
a_{money-meat} & a_{money-bean} & a_{money-rice}\\
a_{calory-meat} & a_{calory-bean} & a_{calory-rice}\\
\end{pmatrix} 
\begin{pmatrix}
x_{meat}\\
x_{bean}\\
x_{rice}\\
\end{pmatrix}
$$

여기서  `요소`인 $a$가 행렬로 정리되고 `요인`인 $x$는 벡터로 나누어 정리되어 깔금하게 보기 좋다. 이것이 바로 행렬과 벡터의 곱이다. 

요소(要素)는 식 계산에 꼭 필요한 성분. 또는 근본 조건을 말하며, 요인(要因)은 
식의 답을 계산하는데 입력변수, 조건등이 될 수 있다. 총 비용과 칼로리를 계산하기 위해선 고기, 콩, 쌀의 그램당 비용과 칼로리를 알아야 하는데 이것이 근본 조건(요소)이다. 우리가 구한 고기, 콩, 쌀의 그램은 변수이자 답을 계산하기 위한 키가 된다. 

좀 더 일반화 하여 표현하면 $m * n$행렬과 $n$차원 벡터에 대해

$$
\begin{pmatrix}
a_{11} & \cdots & a_{1n}\\
\cdots & & \cdots\\
a_{m1} & \cdots & a_{mn}\\
\end{pmatrix} 
\begin{pmatrix}
x_{1}\\
\cdots \\
x_{n}\\
\end{pmatrix} =
\begin{pmatrix}
a_{11}x_{1}+\cdots + a_{1n}x_{n}\\
\cdots \\
a_{m1}x_{1}+\cdots + a_{mn}x_{n}\\
\end{pmatrix}
$$

여기서 주의할 점은 아래와 같다.
* 행렬과 벡터의 곱의 결과는 벡터
* 행렬의 행의 수(세로)는 결과 벡터의 차원
* 행렬의 열의 수(가로)는 계산 대상의 수
* 종벡터($\vec {x}$)를 하나씩 위에서 부터 넘겨 계산

## 행렬은 사상

$n$차원 벡터 $x$에 $m*n$행렬 A를 곱하면 $m$차원 벡터 $y = Ax$가 얻어진다. **즉 행렬 A를 지정하면 벡터를 다른 벡터로 옮기는 사상이 결정된다.(n차원 -> m차원)**  
사실 이것이 행렬에 가잔 중요한 기능이다. 앞으로는 행렬을 보면 단순히 수가 나열되어 있는게 아니라 한 차원의 벡터를 다른 차원으로 변화시키는 사상이 주어졌다고 생각하자. 

마지막으

>사상은 일반적으로 쓰이는 변환이라는 표현도 가능하지만, 수학 용어로서 변환은 '대등한 것에 이동한다'라는 의미다. n차원 공간에서 m차원이라는 다른 세계로 옮기는 것을 변환이라고는 부를 수 없어 사상이라는 더 고차원적인 언어를 사용했다고 한다. 

#력)


# Determinant(행렬식)

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTE5Njk4NjU5LC04NTk5MTU0MzIsLTE1ND
U4MTEyOTcsLTE4MzE3NDk3NTQsNzU2NzQxNzcyLC0xMjU5NDIy
ODgwLC03MDE1MTE0MzAsNDgxOTcyOTgxLDEyNTcyNDc5MzYsMT
MwMDgyMDEwNywtMTEwNjA5Mzg2Miw1NDQxMzYzMF19
-->