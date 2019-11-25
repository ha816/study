# 3장 모든 객체의 공통 메서드

Object는 기본적으로 상속해서 사용하도록 설계되어있다. Object에서 final이 아닌 메서드들(equals, hashCode, toString, clone, finalize)는 모두 재정의(overriding)을 염두에 두고 설계되어 재정의시 지켜야 하는 일반 규약이 명확히 정의되어있다. 

이번 장에서는 final이 아닌 메서드들을 언제 어떻게 재정의해야하는지를 다룬다. 또한 Comparable.compareTo의 경우 Object메서드는 아니지만 성격이 비슷하여 함께 다룬다. 

핵심 키워드
: Object 클래스의 final이 아닌 메서드(equals, hashCode, toString, clone, finalize)

## Item10. equals는 일반 규약을 지켜 재정의하라. 

equals 메서드는 재정의하기 쉬워보이지만 곳곳에 함정이 도사리고 있어 조심해야 한다. 문제를 회피하는 가장 쉬운 방법은 재정의를 하지 않는 것이다. 그냥 두면 **클래스의 인스턴스는 오직 자기자신과만 같게된다.** 따라서 





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc5MTU4MTcyMywtNTgyMzI5NywtMTEyND
E2Mjc2MF19
-->