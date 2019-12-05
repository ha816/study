# 아파치 Commons

Commons Lang 라이브러리는 java.lang 패키지에서 String 객체를 다룰때 발생하는 문제점을 보완하는 몇 가지 라이브러리들이 존재한다. 

# Guava 컬렉션

Guava는 원래 구글이 자바 프로젝트에 이용하려고 개발한 라이브러리 집합이다. 자바 컬렉션 API에서 제공하지 않는 컬렉션들을 지원한다. 

## Multiset

특정 원소의 개수를 셀수있게 만들거나 원소를 일반 집합으로 변경도 할 수 있게 해준다.  컬렉션 라이브러리와 비슷하게 HashMultiset, TreeMultiset, ConcurrentHashMultiset등 몇 가지 구현 클래스가 존재한다.

### HashMultiset
HashMultiset은 분명 set이지만 모든 원소가 다 저장된다. Multi라는 의미는 기능의 확장 정도로 받아드리면 될거 같다. 
```
Multiset<String> strings = HashMultiset.create();
strings.add("1");
strings.add("2");
strings.add("2");
strings.add("3");
strings.add("3");
strings.add("3");

6 == strings.size;
2 == strings.count("2");
Set<String> stringSet = strings.elementSet();
3 == stringSet.size();
```
사실 HashMultiset은 아래와 같이 Map구조로 표현이 가능하다. 맵의 키로는 실제 입력 값들이 들어가고, 대응 하는 값으로 입력 값의 횟수를 저장한다.
```
Multiset<String> strings = HashMultiset.create();
Map<String, Integer> StringToCount = new HashMap();
```

, Multimap 인터페이스

## Collections 유틸리티 클래스

## Iterables 클래스

# Joda Time



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY5MjU1ODE4NCwyNzQ4NDczNzksLTEzNz
A4MDEyMTcsMjEzOTkyODM3LDEzNjk2NTc4OTAsLTEzNjMwODEw
OTBdfQ==
-->