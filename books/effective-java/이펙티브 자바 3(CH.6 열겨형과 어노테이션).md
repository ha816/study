# 열거형(enum)과 어노테이션


자바 1.5에 새로운 참조 자료형이 추가
- 열거 자료형(enum type) 새로운 종류 클래스
- 어노테이션 자료형 새로운 종류 인터페이스

### Item.30 int 상수 대신 enum을 사용해라

열거 자료형(enumerated type) : 고정 개수의 상수들의 값으로 구성되는 자료형 

enum이 도입되기 전에는 통상 int형의 상수들로 열거 자료형을 흉내내었다.

int 형 상수들을 이용한 기법 : int enum 패턴
int enum 패턴의 단점

 - 형 안정성의 불안정함
 - 컴파일 시점 상수(compile-time constant)
	 - 상수 변경시 재컴파일이 필요함
 - 인새 과능한 문자열 변환 어려움 

String 형 상수들을 이용하는 기법 : String enum 패턴
String 패턴은 상수 이름을 화면에 출력하기 용이 그러나 문자열 비교 때문에 성능 하락 

그래서 두 가지 패턴의 문제를 해소한 대안이 도입 -> Enum을 사용

Enum의 아이디어 : 열거 상수(enumeration constant)별로 하나의 객체를 public static final 필드 형태로 제공 

enum 패턴의 장점 

 - 컴파일 시점 형 안정성(compile-time type safety) 보장
 - 컴파일 시점 상수(compile-time constant)이 아니다
 - toString 메서드를 사용하면 쉽게 문자열로 변환 가능

### ordinal 대신 객체 필드를 사용하라

모든 enum은 ordinal 메서드가 있다.
ordinal : enum상수의 위치를 나타내는 정수값을 반환한다. 

enum 상수에 연계되는 값을 ordinal을 사용해 표현하지 말라는 것. 만약 필요하다면 instance field에 저장해야 한다.

public enum Ensemble {
	SOLO(1), 
}

### 32. 비트 필드(bit field) 대신 EnumSet을 사용하라

비트별(bitwise) OR 연산 사용가능
집합을 비트 필드로 나타내면 비트 단위 산술 연산(bitwise arithmetic)을 통해 합집합이나 교집합 등의 집한 연산도 효율적으로 실행 가능. 하지만 비트 필드는 int enum패턴과 동일한 단점 가짐

복수개의 상수를 전달할시 EnumSet 클래스를 사용

### 33. ordinal을 배열첨자로 사용하는 대신 EnumMap을 이용해라 

enum 상수를 어떤 값에 대응시킬 목적이라면 enumMap을 사용하자.

### 34. 확장가능한 enum을 만들어야 한다면 인터페이스를 이용하라

enum 자료형의 단점? enum자료형을 가능. 
대체로 바람직 하지 않지만 ... 

그럴땐 enum 인터페이스를 사용하자

요약 : 계승 가능 enum 자료형은 만들 수 없지만, 인터페이스를 만들고 그 인터페이스를 구현하는 기본 enum 자료형을 만들면 계승 가능 enum 자료형을 흉내 낼 수 있다. 

### 35. 작명 패턴 대신 어노테이션을 사용하라 

자바 1.5 패턴 전에는 도구나 프레임워크가 특별히 취급해야하는 프로그램 요소를 구별하기 위해 작명 패턴(naming pattern)을 사용.

JUnit Test의 경우, testMethod ... 

작명 패턴의 단점들

 - 오타
	 - testSafetyOverride vs tsetSafetyOverride 
 - 특정 요소에 적용이 힘듬
	 - testClass에 Junit이 메서드를 전부 테스트 X
 - 프로그램 요소에 인자를 전달할 마땅한 방법 X

어노테이션은 이러한 문제를 모두 해결!

표식 어노테이션 자료형 선언
import java.lang.annotation.*;

@Retention
@Target 
@Test
...

@Test 어노테이션은 클래스가 동작하는데 영향 X
일반적으로, 어노테이션은 어노테이션이 적용된 프로그램의 동작에는 절대 개입하지 X
testRunner와 같은 프로그램에서 특별히 힌트를 주는 역활

어노테이션이 있으니 더 이상 작명 패턴에 기대지 말자! 

### 36. Ovveride 어노테이션을 일관되게 사용하라

@Override는 메서드 선언할 수 있고, 상위 자료형에 선언된 메서드를 재정의한다는 표현

따라서, 상위 클래스에 선언된 메서드를 재정의 할때는 반드시 선언부에 Overriede를 붙여야 한다.

코드 인스펙션 어노테이션 검사 기능이 있으면 어노테이션으로 경고를 받을 수 있다. 


### 37. 자료형을 정의할 때 표식 인터페이스를 사용하라 



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MzMzNjE2NzYsLTEyNDA4MjQ5NzldfQ
==
-->