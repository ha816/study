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
Long factorial = 1; // Non 재귀
for(int i = 1; i <= n ; i++){ 
	factorial *= i
}
```

# 라이브러리 기능 구현하기

>  애너그램
> 특정 단어와 단어의 목록이 주어진다. 단어의 목록 중에서 특정 단어의 애너그램에 해당하는 단어목록을 모두 구하라. 

애너그램은 단어의 절차를 재배열해서 다른 단어를 만드는 것을 말한다. 규칙은 글자를 삭제해서는 안된다는 것과 멀리 가져다 놓는 것은 가능하다는 것이다.

한 단어의 가능한 어떤 애너그램도 정렬을 하면 모두 같은 단어가 된다. 
```
given a word : w, list of string : L
sortedWord = sw;
for(s : L ) {
	sorted ss = sort(s);
	if(sw.equals(ss)){
	findAnagram()
	} 
}
```

> 문자열 뒤지기를 위한 메서드 작성하라





# 제네릭 사용하기




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAzOTMxODgzOCwzNDg1NjYxMjAsMTM2OT
YyNTUxMyw4NDczNDE1MDEsLTY2ODQ2NjYwNywtMTk3MzUzODYy
MywtMTkyNTQ2NjAxNiw1NTkwMzkwMiwtNzEwNzg2ODMsMTc1OT
E4NzE3Ml19
-->