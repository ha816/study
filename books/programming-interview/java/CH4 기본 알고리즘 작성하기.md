# 빅 오 표현법 살펴보기

# 리스트 정렬하기

>Comparable과 Comparator 인터페이스의 차이는?

자바는 정렬 순서를 정하기 위해서 두 가지 인터페이스를 제공한다. 
Comparable은 비교하려는 객체간의 자연스러운 순서로 정렬할때 사용하고 Comparator는 원하는 대로 정렬 순서를 정하고 싶을 때 사용한다. 

Arrays.sort, Collections.sort 등 정렬 메서드를 사용할때는 정렬할 객체들이 Comparable 인터페이스를 구현해야 한다. 만약 구현하지 않으면 ClassCastException이 발생한다. 

일반적인 순서에 반대되는것 같이 원하는 순서를 정의하고 싶으면 sort 메서드에서 사용할 Comparator 인터페이스에 compare 메서드를  구현해줘야 한다. 

>버블 정렬 알고리즘은 어떻게 구현하는가? 
>삽입 정렬 알고리즘은 어떻게 구현하는가? 
>퀵정 정렬 알고리즘은 어떻게 구현하는가?
>병항 정렬 알고리즘은 어떻게 구현하는가? 

# 리스트 검색하기

>이진 검색은 어떻게 구현하는가?

이진 검색(binary search)는 이미 정렬된 원소로 구성된  리스트가 주어졌을때, 내가 찾고자하는 원소가 어디에 있는지 시간 복잡도 O(log n)으로 찾는 방법이다.  

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExNzE1MTA0OTddfQ==
-->