
## 8장 일반적인 프로그래밍 원칙

지역변수, 제어구조, 라이브러리 사용, 다양한 자료형 사용, 그리고 리플렉션과 네이티브 메서드 그리고 마지막으로 최적화 작명 관습에 대해 살펴본다. 

### 45. 지역변수의 유효범위를 최소화하라 

클래스와 멤버의 접근 권한은 최소화 하자와 유사하다. 

지역변수의 유효범위 최소화하는 가장 강한 기법은, **처음 사용하는 곳에서 선언하는것!**

지역변수를 너무 빨리 선언하면 범위가 너무 확장되고 변수를 사용할때 쯤 변수가 무엇이었는지 초기값이 뭐였는지 잊을 수 있다. 또 특정 블록 밖에서 선언된 변수는 사용하고자 했던 곳 이외의 장소에서 실수로 사용 될 수도 있다.

거의 모든 지역변수 선언에는 초기값이 포함되어야 한다. 

순환문(loop)를 잘쓰면 유효범위를 최소화 할 수 있다. for문이나 for-each문의 경우, 순환문 변수(loop variable)을 선언 할 수 있는데, 그 유효범위는 선언된 지역(for 다음에 오는 순환문 괄호 ()와 순환문 몸체{} 내부의 코드)안으로 제한

따라서 사실 while문 보다는 for문을 쓰는 것이 좋다. 

// 컬렉션 순회시 바람직한 숙어
for( Element e : collections) {
... 
}

Iterator<Element> i = c.iterator();
while(i.hasNext()){
}

Iterator<Element> i2 = c2.iterator();
while(i.hasNext()){ //실수로 i2가 아닌 i를 순회함
}

for(int i =0, n = Integer.MAX; i < n ; i ++){
}

위의 for문은 두개의 순환문 변수가 사용되었다. 변수 i, n은 유효범위는 for문 안으로 제한된다. 순환문의 각 단계가 달라지지 않으면 이 패턴대로 코딩을 하자

마지막으로, 유효범위 최소화 전략은 메서드의 크기를 줄이고 특정한 기능에 집중해라. 

서로 다른 두 기능을 한 메서드안에 넣으면 한 기능을 수행하는데 필요한 지역 변수의 유효범위가 다른기능까지 확장된다. 나누어 구현하자.

### 46. for문보다는 for-each문을 사용해라 

자바 1.5부터 도입된 for-each 문은 성가신 코드를 제거하고 반복자나 첨자변수를 제거해서 오류가능성을 줄인다. 

for-each문은 코드를 읽기도 쉽고 어떤 상황에서는 for문 보다도 성능이 좋다. 

for-each문은 컬렉션과 배열뿐만 아니라 Iterable 객체도 순회 가능하다. 

뭘해도 for-each를 쓰는게 좋지만 세 경우네는 for-each문을 적용할 수 없다. 

 1. 필터링 : 순회하다가 특정 원소를 삭제할 필요가 있다면, 반복자를 명시적으로 써야한다. 반복자의 remove 함수를 사용해야 하기 때문에 
 2. 변환 : 순회하다가 원소 가운데 일부 또는 전부의 값을 변경해야 하면, 값을 수정하기 위해 리스트 반복자나 배열 첨자가 필요하다. 
 3. 병렬 순회 : 여러 컬렉션을 병력적으로 순회하고, 모든 반복자나 첨자 변수를 발맞춰 나가도록 구현한다면 명시적으로 제어를 해야 한다. 

### 47. 어떤 라이브러리가 있는지 파악하고 적절히 활용해라.

우리가 필요한 기능을 구현하는데 라이브러리가 있다면? 

세 가지 장점이 있다. 

 1. 굳이 라이브러리의 소임을 하는지 알 필요가 없다.
 2. 실제 필요한 기능과 관계가 없는 문제를 해결한다고 자원을 낭비할 필요가 없다. 
 3.  라이브러리가 업데이트 되면서 성능이 점차 개선된다. 
 4. 표준 라이브러리를 쓰면 주류 개발자들과 같은 코드를 접할 가능성이 높아진다. 

라이브러리는 규모가 너무 방대해서 모든 문서를 공부하기는 힘들지만 ...
자바 프로그래머라면 java.lang, java.util, java.io 정도는 잘 알아둬야 한다. 
java.util의 컬렉션 프레임워크
java.util의 병행성 유틸은 공부할 필요가 있다. 

concurrent는 다중 스레드를 짜는데 도움을 주는 유틸

중요한 새로운 릴리스(major new release)때 새로운 기능이 추가 되는데, 그때마다 무엇이 추가되었는지를 알면 좋다. 

요약하자면, 바퀴를 다시 만들지 말자(don't reinvent the wheel) 

### 48. 정확한 답이 필요하다면 float와 double을 피해라 

float과 double은 기본적으로 관학 또는 엔지니어링 계산에 쓰일 목적으로 설계된 자료형이다. 

float과 double은 이진 부동 소수점 연산(binary floating-point arithmetic)을 수행하고 이는 넓은 범위의 값(magnitude)에 대해 정확도가 높은 근사치를 제공한다. 

즉 정확한 결과(exact)를 제공하진 않기 때문에 정확한 결과가 필요한 곳에서는 사용 하면 안된다. 

돈 계산시에는 BigDecimal, int 또는 long을 사용해서 계산을 하는 원칙을 지키자 . 

BigDecimal의 단점은 기본 산술인자 자료형(primitive arithmetic type) 보다 사용이 불편하고 느리다. 

요악하면, 정확한 답을 요구하는 문제에선 float이나 double을 쓰지 말자. 

소수점 이하의 처리를 시스템이 알아서, 불편해도 성능이 조금 떨어져도 문제 없을때 BigDecimal을 쓰자. 

성능이 중요하고 소수점 아래 수를 직접 관리하고 계산할 수가 심하게 크지 않을때는 int나 long을 쓰자. 
관계된 수치들이 십진수 9개 이하 일땐 int
십진수 18개 이하일땐 long을 쓰자. 
그 이상일때 BigDecimal을 쓰자.

### 49. 객체화된 기본 자료형 대신 기본 자료형을 이용하자 

기본 자료형 : int, double, boolean 등 
참조 자료형 : String, List 등
모든 기본 자료형에는 대응하는 참조 자료형이 있다. 이를 객체화된 기본 자료형이라 한다.

객체화된 기본 자료형(boxed primitive type)  

자바 1.5에서 자동 객체화(auto boxing)와 자동 비객체화(auto unboxing)가 추가되었다. 
-> 객체화된 기본 자료형과 자료형이 자동으로 아주 가까운 사이가 되었다. 하지만 그 주요한 근본적이 차이는 존재하기 때문에 조심하자  

세가지 차이점

1. 기본형은 값 그자체만 가진다. 객체 기본형은 값 뿐만 아니라 신원(identity)를 가진다. 즉 같은 값을 가지는 객체 기본형이더라도 다른 객체라는 것
2. 기본형은 null이 없지만 객체 기본형은 null이 있다.
3. 기본형은 연산이 빠르지만, 객체 기본형은 느리다.

객체 기본형을 비교할때는 항상 조심해야 한다. 
**객체화된 기본 자료형에 == 연산자를 쓰는 것은 거의 항상 오류!**

기본 자료형과 객체화된 기본 자료형을 한 연산 안에 묶어 놓으면 기본적으로 객체화된 기본 자료형을 기본자료형으로 변환한다. 그리고 이 과정에서 NPE 예외가 발생할 수 도 있다. 

요약하면, **기본적으로 기본자료형을 사용하자!** 

컬렉션에 넣을때는 어쩔수 없이 객체화된 기본자료형을 넣지만 ... 

###50. 다른 자료형이 적절하다면 문자열 사용은 피해라

문자열 값은 자료형을 대신하기에는 부족하다. 
-> 적당한게 없으면 새로 만들자

문자열은 enum 자료형을 대신하기에는 부족하다.
-> 열거 자료형 상수가 훨씬 좋은데 ....

문자열은 혼합 자료형(aggregate type)을 대신하기에 부족하다. 
-> 써먹으려면 파싱을 해야 하는데.... 귀찮

문자열은 권한(capability)을 표현하기에 부족하다. 
-> 문자열을 사용해서 기능 접근 권한을 이용한 적이 있는데 ...
-> 문제는 문자열이 스레드의 지역변수의 전역적인 이름공간(global namespace)라는 것..
-> 만약 2개 이상의 클라이언트가 같은 지역변수명을 쓴다면 동일한 변수를 공유하게 되어 문제 발생
또한 의도적으로 같은 문자열을 사용하면 다른 클라이언트의 정보에 접근이 가능 

요약하자면, 더 적합한 자료형이 있으면 가능한 객체를 문자열로 표현하는것은 피해라 

### 51. 문자열 연결 시 성능에 주의해라

문자열 연결(concatenation) 연산자 +는 여러 문자열을 하나로 합치는데 편리한 수단. 

몇개 정도의 문자열을 합치는데는 문제가 없지만 많은 문자열을 합치면 성능상에 문제가 발생.

n개의 문자열에 연결 연산자를 반복 적용하면 시간 복잡도가 n^2 이다. 
-> i번째 문자열은 n-i+1 번 복사된다. 
-> i가 1이면, n번 복사된다

= n + n-1 + n-2 + ... + 1 대략 n^2

따라서 만족스러운 성능을 얻으려면 String 대신 StrinbBuilder나 StringBuffer를 쓰자. 

StringBuilder의 경우, 넉넉한 배열을 잡고 거기에 복사를 하는 방식이기 때문에 복잡도가 n

따라서, 성능이 걱정된다면 많은 문자열을 연결할때 + 연산자는 피하자. 대신 StringBuilder의 append메서드를 사용하자.

-> +연산자를 써도 내부적으로 StringBuilder를 사용한다고 하지만... 매번 새로운 StringBuilder를 만들기 때문에 성능적으로 약간 느림

### 52. 객체를 참조할 때는 그 인터페이스를 사용하라 

규칙 42는 인자의 자료형으로 클래스 대신 인터페이스를 사용할것! 

적당한 이너페이스 자료형이 있다면 인자, 반환값, 변수 그리고 필드의 자료형은 클래스 대신 인터페이스로 선언 하자! 

인터페이스를 자료형으로 쓰면 프로그램은 더욱 유연해진다. 

적당한 인터페이스가 없을 경우 객체를 클래스로 참조하는 것은 당연하다. 

### 53. 리플렉션 대신 인터페이스를 이용하라 

java.lang.reflect의 핵심 리플렉션 기능(core reflection facility)를 이용하면 메모리에 적재된(load) 클래스의 정보를 가져오는 프로그램을 작성할 수 있다. 

클래스 객체가 주어지면 해당 객체가 나타내는 클래스의 생성자, 메서드, 필드등을 나타내는 Constructor, Method, Field 객체들을 가져올 수 있다. 이 객체들로 클래스의 멤버 이름이나, 필드 자료형, 메서드 시그니처 등의 정보를 알 수 있다. 

Constructor, Method, Field를 사용하면 생성자, 메서드, 필드를 반영적으로 조작 할 수 있다. 객체 생성, 메서드 호출, 필드에 접근도 가능.  

하지만 이런 능력에는 대가가 필요

* 컴파일 시점에 자료형을 검사함으로서 얻는 이점 포기
	* 리플렉션을 통해 알수 없는 메서드를 호출하면 실행 중 오류 발생 -> 각별히 주의
* 리플렉션 기능을 이용하는 코드는 보기 싫은데다 가독성도 떨어진다.
* 성능이 낮다. 리플렉션을 통한 메서드 호출 성능은, 일반 메서드 호출에 비해 훨씬 낮다. 

원래 리플렉션은 컴포넌트 기반 응용 프로그램 저작 도구를 위해 설계된 기능.
보통 요청에 따라 클래스를 메모리에 올리고, 리플렉션으로 어던 메서드와 생성자가 지원되는지 알아낸다. 

**명심할 것은, 일반적은 프로그램은 실행중에 리플렉션을 통해 객체를 이용하려고 하면 안된다.** 

리플렉션을 아주 제한적으로 사용하면 오버헤드는 피하면서 리플렉션의 장점을 누릴 수 있다. 컴파일 시점에 없는 클래스를 이용해야 하는 프로그램 중에는, 해당 클래스 객체를 참조하는데 사용할 수 있는 인터페이스나 상위 클래스는 컴파일 시점에 이미 갖추고 있다. 이럴때 객체 생성은 리플렉션으로 하고 객체 참조는 인터페이스나 상위 클래스를 통해 하면된다. 
P312 참조

요약 : 리플렉션은 특정한 종류의 복잡한 시스템 프로그래밍에 필요한, 강력한 도구다. 하지만 단점이 많다. 컴파일 시점에는 알수 없는 클래스를 이용하는 프로그램을 작성한다면 리플렉션을 사용하되 가능하면 객체를 만들때만 사용하고, 객체를 참조할때는 컴파일 시에 알고 있는 인터페이스나 상위 클래스를 사용하자 .

### 54. 네이티브 메서드는 신중하게 사용하라 

네이티브 인터페이스(Java native interface, JNI)는 c나 c++ 등의 네이티브 프로그래밍 언어로 작성된 네이티브 메서드를 호출하는데 이용하는 기능이다. 네이티브, 메서드가 수행하는 계산은 네이티브 언어로 실행되며, 자바 언어로 전달된다. 

전통적으로 세 가지 용도로 쓰임.

 - 레지스트리나 파일락 같은, 특정 플랫폼에 고유한 기능을 이용이 가능
 - 이미 구현되어 있는 라이브러리를 이용이 가능하다.
 - 성능이 중요한 부분의 처리를 네이티브 언어에 맡긴다. 

그러나! **네이티브 메서드를 통해 성능을 개선하는 것은 비추천!** 
-> 현재 JVM은 빠르다.
-> 네이티브 메서드를 사용하는 프로그램은 메모리 훼손 문제로 부터 자유롭지 않다. 
-> 디버깅도 어렵다. ...

요약하자면, 네이티브 메서드를 사용하는것은 별로다. 

### 55. 신중하게 최적화하라

최적화 관련 세가지 격언 

* 어떤 다른 이유보다, 효율성이라는 이름으로 저질러지는 죄악이 더 많다(효율성을 성취하는 것도 아닌데 ... )
* 작은 효율성(small efficiency)에 대해서는, 말하자면 97%정도는 잊어버려라. 섣부른 최적화(premature optimization)는 모든악의 근원이다.
* 등등

성능 때문에 구조적인 원칙(architectural principle)을 희생하지 마라. 빠른 프로그램이 아닌 좋은 프로그램을 만들려 노력해라. 
좋은 프로그램을 정보 은닉 원칙을 지킨다. 
설계에 관한 결정은 각 모듈 내부적으로 한다. 

설계를 할땐 성능을 제약할 가능성이 있는 결정들은 피하라. 
모듈간 상호작용을 하는 부분은 성능에 문제가 있다는 사실을 발견 후 고치기 가장 어렵다. 

API 설계할때 내리는 결정들이 성능에 어떤 영향을 끼칠지 생각하라. 

최적화를 시도할때 마다, 전후 성능을 측정하고 배교하라. 의외로 코드 개선을 성능이 개선되지 않는 경우가 많다. 

요약하자면, 빠른 프로그램을 만들기 위해 노력하지는 말라 대신 좋은 프로그램을 짜기 위해 노력하자. 성능은 따라온다. 

성능이 느리다면, 프로파일링 도구의 도움을 받아 최적화한다. 
-> 알고리즘 검토
-> 저수준 검토

### 56. 일반적으로 통용되는 작명 관습을 따라라

작명관습(naming convention)은 크게 두 가지 범주로 나눌 수 있다. 철자 VS 문법

-- 철자에 관한 작명관습
패키지, 클래스, 인터페이스, 메서드, 필드 그리고 자료형 변수에 관한 것으로, 그 양이 얼마 안된다. 

이 철자를 어기면 API 사용도 힘들다. 
-> 꼭 지키자... 

**패키지 이름은 마침표를 구분점으로 사용하는 계층적 이름이다.** 

패키지 각각 컴포넌트는 알파벳 소문자, 숫자는 거의 사용하지 않는다. 

패키지명 컴포넌트는 짧아야하며, 보통 여덜문자 이하다. 의미가 확실한 약어를 활용하면 좋다. Utils -> util, awt 같은 두문자(acronym)도 사용가능. 

Enum이나 애노테이션 자료형, 클래스나 인터페이스 이름은 하나 이상의 단어로 구성 
단어의 첫 글자는 대문자

max나 min처럼 약어를 쓰는것을 제외하면 약어 사용은 피해야한다. 

메서드와 필드 이름은 클래스나 인터페이스 이름과 동일한 철자 규칙을 따른다. 다만 첫 글자는 소문자로 한다. 

유일한 예외로 상수 필드는 단어사이에 언더바(_)를 둔다

지역변수 이름은 멤버 이름과 같은 철자 규칙을 따른다. 

식별자 자료형 표 참조

|식별자 자료형| 예제  |
|--|--|
|패키지  |com.google.inject, org.joda.time.format  |
|클래스, 인터페이스|Timer, FitireTask, LinkedHashMap, HttpServlet|
|메서드나 필드  | remove, ensureCapacity, getCrc|
|상수 필드 |MIN_VALUE, NEGATIVE_INFINITY| 
|지역 변수 |xref, house number| 
|자료형 인자 |T, E, K, V, X, T1, T2| 

메서드는 일반적으로 동사나 동사구를 이름으로 갖는다. 
Boolean의 경우, is 드물게는 has
그 뒤에는 명사나 명사구, 또는 형용사나 형용사구가 붙는다. 
isDigit, isProbablePrime, ... 

Boolean이 아닌 경우 또는 객체 속성을 반환하는 메서드에는 보통 명사나 명사구, 또는 get으로 시작하는 동사구를 이름으로 붙인다. 
size, hashCode, getTime

빈 클래스의 경우 메서드 이름은 반드시 get 또는 set으로 해야 한다. 

특별한 메서드 이름 
toType : 객체의 자료형을 변환하는 메서드 또는 다른 자료형의 객체를 반환하는 메서드

asType : 인자로 전달받은 객체와 다른 자료형의 뷰(view)객체를 반환하는 메서드, asList

typeValue : 호출대상 객체와 동일한 기본 기본형 값을 반환, intValue

valueOf, of, getInsatance, new Instance, getType, newType : 정적 팩터리 메서드에는 이와 같은 이름

|정적 팩터리 메서드| 설명  |
|--|--|
|valueOf  |인자로 주어진 값과 같은 값을 가지는 객체를 반환한다, 형변환(type-conversion) 메서드|
|of|valueOf의 축약형|
|getInstance|인자에 기술된 기술객체를 반환하지만 같은 값을 가지지 않을수도 있다. |
|newInstance |getInstance와 같지만 호출때마다 다른 객체 반환| 
|getType|getInstance와 같지만, 반환될 객체의 클래스와 다른 클래스에 팩터리 메서드가 있을 경우 상용한다, Type은 팩터리 메서드가 반환할 객체 자료형| 
|newType |newIstance와 같지만 반환될 객체의 클래스와 다른 클래스에 팩터리 메서드가 있을때 사용한다. Type은 팩터리 메서드가 반환할 객체 자료형

필드는 굉장히 느슨한 규칙을 따른다. 왜냐하면 잘 설계된 API에서는 외부로 공개된 필드고 별로 없기 때문이다. 
boolean형의 필드에는 보통 boolean메서드의 같은 이름을 붙이나, 접두어 is는 생략한다. 
isInitialized, isComposite -> initalized, composite

요약하자면, 표준적 작명관습(standard code convention)을 지키자! 























 

> Written with [StackEdit](https://stackedit.io/).
