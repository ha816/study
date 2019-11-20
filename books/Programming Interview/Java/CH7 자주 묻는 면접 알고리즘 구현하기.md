# 피보나치 수열 구하기
> 1에서 n까지의 피보나치 수열을 반환하는 메서드를 정의하라

$L(k) = L(k-2) + L(k-1)$, $L(1) = L(2) = 1$

```
function fibonachi(int n){
	if(n == 1 || n == 2){
		return 1;
	}
	return fibonachi(n-1) + fibonachi(n-2);
}
```

# 팩토리얼 구하기

# 라이브러리 기능 구현하기

# 제네릭 사용하기




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzMzMwNDUxMjQsMTc1OTE4NzE3Ml19
-->