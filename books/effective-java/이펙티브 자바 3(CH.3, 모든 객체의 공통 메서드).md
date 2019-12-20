# 3장 모든 객체의 공통 메서드

Object는 기본적으로 상속해서 사용하도록 설계되어있다. Object에서 final이 아닌 메서드들(equals, hashCode, toString, clone, finalize)는 모두 재정의(overriding)을 염두에 두고 설계되어 재정의시 지켜야 하는 일반 규약이 명확히 정의되어있다. 

이번 장에서는 final이 아닌 메서드들을 언제 어떻게 재정의해야하는지를 다룬다. 또한 Comparable.compareTo의 경우 Object메서드는 아니지만 성격이 비슷하여 함께 다룬다. 

핵심 키워드
: Object 클래스의 final이 아닌 메서드(equals, hashCode, toString, clone, finalize)

## Item10. equals는 일반 규약을 지켜 재정의하라. 

equals 메서드는 재정의하기 쉬워보이지만 곳곳에 함정이 도사리고 있어 조심해야 한다. 문제를 회피하는 가장 쉬운 방법은 재정의를 하지 않는 것이다. 그냥 두면 **클래스의 인스턴스는 오직 자기자신과만 같게된다.** 따라서 아래에서 열거한 상황 중 하나에 해당한다면 재정의 하지 않는 것이 최선이다.

* 각 인스턴스가 본질적으로 고유하다. 값을 표현하는게 아니라 동작하는 개체를 표현하는 클래스가 해당된다. Thread가 좋은 예로, Object의 equals 메서드는 이러한 클래스에 딱 맞게 구현되었다.
* 인스턴스의 논리적 동치성(logical equality)를 검사할 일이 없다. 예를 들어 regex.Pattern은 equals를 재정의해서 Pattern의 인스턴스가 같은 정규표현식을 나타내는지 검사하는, 즉 논리적 동치성을 검사하는데 애초에 이 방식이 필요하지 않다고 판단되면 Object의 기본 equals만으로도 해결된다.
* 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 맞는다. 
* 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다. 여러분이 위험을 철저히 하고 싶어서 equals가 실수로 라도 호출되는 걸 막고 싶다면 아래와 같은 코드를 넣자

```
@Override public boolean equals(Object o){
	throw new AssertionError();
}
``` 

자 그럼 equals를 재정의 할땐 언제일까? 
**객체 식별성(object identify; 두 객체가 물리적으로 같은가)이 아니라 논리적 동치성(logical identify)를 확인해야하고 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의 되지 않았을때다.** 

주로 값 클래스들이 여기에 해당한다. 값 클래스는 Integer와 String 같은 값을 표현하는 클래스를 말한다. 두 객체를 equals로 비교하는 프로그래머는 두 객체가 같은지가 아니라 객체의 값이 같은지를 알고 싶을것이다. 

논리적 동치성을 확인하도록 재정의 해두면, Map의 키와, Set의 원소로 사용할 수 있게 된다. 값 클래스라고 해도, 값이 같은 클래스가 둘 이상 만들어지지 않음을 보장하는 클래스라면 equals를 재정의 하지 않아도 된다. 이런 클래스는 당연히 논리적으로 같은 인스턴스가 2개이상 만들어지지 않으니 논리적 동치성과 객체 식별성이 같다. equals 메서드를 재정의할때는 반드시 일반 규약을 따라야한다. 다음은 Object명세에 적힌 규약이다. 

equals 메서드는 동치관계(equivalence relation)을 구현하며 아래 조건을 만족해야 한다. 

* 반사성(reflexivity) : null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true.
* 대칭성(symmetry) : null이 아닌 모든 참조 값 x,y에 대해 x.equals(y) == y.equals(x)를 만족한다.
* 추이성(transitivity) : null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y), y.equals(z)가 true이면 x.equals(z)도 true다.
* 일관성(consistency) : null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)를 반복해서 호출해도 항상 true이거나 false이다. 
* null-아님 : null이 아닌 모든 참조값 x에 대해, x.equls(null)은 반드시 false이다. 

수학에 익숙하지 않다면 다소 어려울수 있지만 굉장히 중요한 내용이다. 컬렉션에서는 여러 객체를 전달 받는데 equals 규약을 지킨다고 가정한다. 만약 equals를 제대로 지키지 않은 상태의 객체가 컬렉션에 들어간다면 어떻게 동작할지 예측이 어렵다.

먼저 Object 명세에서 말하는 동치 관계란 무엇일까? 쉽게 말해 집합을 서로 같은 원소들로 이루어진 부분집합으로 나누는 연산이다. 이 부분 집합을 동치류(equivalence class; 동치 클래스)라 한다. equls 메서드가 쓸모있으려면 모든 원소가 같은 동치류에 속한 어떤 원소와도 서로 교환할 수 있어야 한다. 

동치 관계를 만족하기 위한 5가지 요건











> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0NTUzMzYxMzgsLTYxNzEyMDg2LDEyOD
AxODAwNDYsMTU5NzU0NDk0LC03MTY1ODYwNzcsLTIwNzMwNzc2
OTgsLTkyODM1MTkwNSwtNjgxMTExMzcxLC0xODUzNjMxMzQ1LD
E3OTE1ODE3MjMsLTU4MjMyOTcsLTExMjQxNjI3NjBdfQ==
-->