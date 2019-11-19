# 3장 모든 객체의 공통 메서드

Object는 기본적으로 상속해서 사용하도록 설계되어있다. Object에서 final이 아닌 메서드들(equals, hashCode, toString, clone, finalize)는 모두 재정의(overriding)을 염두에 두고 설계되어 재정의시 지켜야 하는 일반 규약이 명확히 정의되어있다. 

이번 장에서는 final이 아닌 메서드들을 언제 어떻게 재정의해야하는지를 다룬다. 또한 Comparable.compareTo의 경우 Object메서드는 아니지만 성격이 비슷하여 함께 다룬다. 

핵심 키워드
: Object 클래스

##


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTUzODM5MTUxXX0=
-->