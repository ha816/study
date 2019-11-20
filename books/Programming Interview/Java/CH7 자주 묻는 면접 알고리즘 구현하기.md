# 피보나치 수열 구하기

> 피보나치 수열의 n번째 값을 구하는 메서드 작성

$L(k) = L(k-2) + L(k-1)$, $L(1) = L(2) = 1$

```
function fibonachi(int n){
	if(n == 1 || n == 2){
		return 1;
	}
	return fibonachi(n-1) + fibonachi(n-2);
}
```
그런데 이렇게 하면 많이 느릴거야 ...
동적계획법으로 캐시를 걸어서 반복되는 계산을 메모리에 두자

```
cacheMap
function fibonachi(int n){
	if(n == 1 || n == 2){
		return 1;
	}
	if(cache.contain(n)){	
		return cache
	}
	int result = fibonachi(n-1) + fibonachi(n-2);
	cache.put(n, result);
	return result;
}
```

# 팩토리얼 구하기

> 재귀적 방법을 사용하지 않는 팩토리얼을 작성하라

$n! = 1*2*\cdots n$

```
function factorial(int n){ //재귀버전
	if(n == 1){
		return 1;
	}
	return factorial(n-1) * n;
}
```

```
Long factorial = 1; //재
for(int i = 1; i <= n ; i++){ 
	factorial *= i
}
```



# 라이브러리 기능 구현하기

# 제네릭 사용하기




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbODY5NjUyMjE3LC03MTA3ODY4MywxNzU5MT
g3MTcyXX0=
-->