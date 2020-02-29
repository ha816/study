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
요소는 

>행렬은 사상이다?
>사상은 일반적으로 쓰이는 변환이라는 표현도 가능하지만, 수학 용어로서 변환은 '대등한 것에 이동한다'라는 의미다. n차원 공간에서 m차원이라는 다른 세계로 옮기는 것을 변환이라고는 부를 수 없어 사상이라는 더 고차원적인 언어를 사용했다고 한다. 

## Determinant(행렬식)

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA1ODEyNDgxMiw1NDQxMzYzMF19
-->