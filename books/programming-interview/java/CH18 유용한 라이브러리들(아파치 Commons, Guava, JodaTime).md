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

## Multimap 인터페이스

Multimap 인터페이스는 Multiset과 유사하며 Map 구현에서 키가 한번 이상 될 수 있다. 

한 키에 대해서 중복되는 값이 있다면 이를 저장하기 위해 Map 구현을 쓸때는 일반적으로 중복되는 값들을 List나 Collection으로 저장을 한다. 그러면 아래와 같은 의사코드를 자주 사용하게 된다.
```
method put(key, value){
	if(map contains key){
		let collection = map(key);
		collection.add(value);
	} else {
		let collection = new List;
		collection.add(value);
		map.put(key, collection);
	}
}
```
위 코드는 불확실하고 에러가 발생하기 쉬우며 스레드 세이프한지 생각해봐야한다. 이때 Multimap을 스면 이러한 문제를 해결해준다.

```
Multimap<String, String> mapping = HashMultimap.create();
mapping.put("1", "Smith");
mapping.put("1", "Simon");
mapping.put("2", "Min");
Collection<String;> one = mapping.get(1)
2 == one.size();
1 == mapping.get(2).size();
```
HashMultimap의  get 메서드는 Collection 객체를 반환한다는 것을 기억하자.
Multimap 인터페이스는 자바 Map과 약간의 차이가 있다. 
get 메서드는 키로 값을 얻으려면 null 을 반환하지 않고 빈 컬렉션을 반환한다. 

## BiMap 인터페이스

**BiMap은 맵의 또다른 타입으로 양방향 조회를 가능하게 하는 인터페이스이다. 양방향이란 키로 값을 조회할수 있고 반대로 값으로 키를 조회한다는 이야기다.** 자바의 Map으로 이를 구현하는건 굉장히 까다롭다. 하지만 BiMap을 쓰면 쉽다. 
```
BiMap<String, String> stockToCompany = HashBiMap.create();
BiMap<String, String> companyToStock= stockToCompany.inverse();

stockToCompany.put("GOOGLE", "GOOG");
stockToCompany.put("APPLE", "APPL");

"GOOGLE" = stockToCompnay.get("GOOG");
"AAPL" = companyToStock.get("APPLE");
```

## Collections 유틸리티 클래스

자바 컬력센 API의 Collections 유틸리티 클래스는 수정할 수 없는 컬렉션을 생성하는데 필요한 몇 가지 유틸리티 메서드를 제공한다. 
```
fianl List<Integer> unmodifiableNumbers = Collections.unmodifiableList(numbers);


```



## Iterables 클래스

# Joda Time



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzMzY4NzE2ODgsNzM4MDExOTgyLDE4Mz
Q1MTg2ODEsLTEzMzM1Nzc2MDksMjc0ODQ3Mzc5LC0xMzcwODAx
MjE3LDIxMzk5MjgzNywxMzY5NjU3ODkwLC0xMzYzMDgxMDkwXX
0=
-->