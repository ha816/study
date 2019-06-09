## 람다와 스트림(Lambdas and Streams)

자바 8에서, 함수적 인터페이스, 람다 그리고 메서드 참조가 함수 객체를 쉽게 만들기 위해 추가 되었다. 

스트림은 이러한 언어 변화에 따라 일련의 데이터를 처리하는 라이브러리 서포트를 제공하기 위해  추가되었다.

### anonymous 클래스를 만드는데 람다를 써라 
역사적으로 과거에는 , 인터페이스 싱글 추상 메서드는 함수 타입으로 사용되었는다.
이 인터페이스의 객체는 함수 객체로 알려졌고 함수와 행동을 나타냈다. 
JDK 1.1에  Anonymous 클래스가 생겼다. 

// Anonymous class instance as a function object
new Comparator<String>() {
	public int compare(String s1, String s2) {
	return Integer.compare(s1.length(), s2.length())
	}
}

anonymous 클래스는 함수 객체를 전략 패턴으로 활용하는 객체 지향 디자인에 적합하다. 
자바 8, 싱글 추상 메서드에서 개념을 형식화 하였다. 이러한 인터페이스는 함수적 인터페이스로 알려졌고 자바 언어는 람다 표현식을 사용해서 이러한 인터페이스를 만들 수 있게 만들었다. 람다 익스프레션 = 람다스 라고 줄여서도 한다. 

앞서 했던 anonoymous class보다 훨씬 람다 표현은 훨씬 정확하고 람다표현으로 대체 되었다.

// lambda expression as function object
Collections.sort(words, (s1,s2) -> Integer.compare(s1.length(), s2.length()); 
주의할 점은 람다의 타입(Comparater<String>), 그리고 리턴 타입은 코드에 표현되어 있지 않다. 
그래서 컴파일러는 타입 추론(type inference)으로 문맥으로부터 이런 타입을 불러온다. 
가끔 컴파일이 타입을 알수가 없어 정의를 결정해줘야 할때도 있다. 
타입 추론의 규칙은 복잡하다; 사실 알 필요 없다. 

모든 람다 파라미터의 타입을 생략은  프로그램을 깔끔하게 만들어 준다. 

comparator 생성자 메서드가 람다의 자리에 사용되면 comparator의 단편은 훨씬 간결해진다.

Collections.sort(words, comparingInt(String::length); 
사실 이 단편은 자바 8의 리스트 인터페이스에 추가된 sort 메서드를 통해 더 짧아지는 이점이 있다.

words.sort(comparingInt(String:: length);
 
 또 다른 예로 책에서 ENUM을 Operation을 보면, 
//  Enum type with constant-specific class bodies & data 
public enum Operation {
	PLUS("+") {
	public double applry(double x, double y) { return x + y;}
	}, 
	...
	private fianl String symbol;
	Operation(String symbol) { this.symbol = symbol; }
	public abstract double apply(double x, double y)
}
enum 객체 필드는 constant-specific 클래스 바디로 참조 되는데, 람다를 사용하면 이런  constant-specific 활동을 쉽게 구현할 수 있다. 

// Enum with function object fields & constant-specifinc behaviour
public enum Operation {
	PLUS ( "+", (x, y) -> x + y),
	MINUS ( "-", (x, y) -> x + y),
	...
	private final String symbol;
	private final DoubleBinaryOperator op;
	Operation(String symbol, DoubleBinaryOperator op){
	this.op = op; this.symbol = symbol;
	}
	public double applry(double x, double y) {
		return op.applyDoulbe(x,y)
	}
}

중요한 것은 DoubleBinaryOperator 인터페이스를 enum 상수 행동을 표현하기 위해 사용했다는 것. 
DoubleBinaryOperator는 java.util.function에 미리 정의된 인터페이스고 두개의 double 인수를 받아 double 결과를 반환하는 함수를 나타낸다. 

메서드 클래스와는 다르게 람다 표현은 이름과 문서화가 부족하다; 만약 계산이 몇라인 안되게 간단하지 않고 명료하지 않다면 람다로 표현하지 마라! 
단순히 한 문장이 람다로 표현하기 이상적이다. 3줄 정도가 최대이다. 
만약 이 규칙을 어기면, 가독성이 무지 하게 떨어진다. 

anonymous 클래스를  람다의 구식이라고 생각할지도 모른다. 진실에 가깝기는 하지만,  람다로는 해결못하는 몇 가지를 anonymous 클래스로는 가능하다. 

람다는 함수적 인터페이스에 제약이있다. 추상 클래스를의 객체를 만들려고 하면 anonymous 클래스로는 가능하지만 람다는 안된다. 
마지막으로, 람다는 스스로를 참조 할수가 없다. 

람다에서 this 키워드는 전형적으로 원하는 가까운 객체를 참조한다. anonymous class에서는  this키워드는 anonymous cclass를 참조한다. 
즉 만약 클래스의 바디에서 함수 객체를 접근하려고 하면 anonymous class를 
써야한다. 

요약하자면, 자바 8에 람다는 작은 함수 객체를 표현하는데 최고의 방법이다. 
그러나 함수적 인터페이스가 아닌 객체 타입을 반드시 만들어야하지 않는다면 함수 객체를 위해 anonymous class를 사용하진 말자! 

또한 람다는 작은 함수 객체를 표현하는데  무척 쉽고 자바가 그전까지 없던 함수적 프로그래밍의 기술을 위한 새로운 열린 문이다.

### Prefer method references to lambdas(

람다의 anonymous class를 넘어 근본적인 장점은 단편적이라는 것이다!

**method references** : 함수 객체를 만드는데  람다 보다 더 간편한 방법! 
 
예를 들면, map.merge( key, 1, (count, incr) -> count + incr);

merge method의 경우, 자바 8에서 Map interface에 추가되었다. 
만약 주어진 키에 맞는 키가 없으면, 주어진 값을 넣고 주어진 키가 없으면 현재 상태의 값과 주어진 값으로 함수를 실행한다. 그리고 결과로 계산된 값을 덮어쓴다. 

매우 읽기 좋지만 획일적이고 상세하다. 파라미터 count와 incr 많은 값을 추가하지 않고, 동등한 공간을 차지한다. 
모든 람다는 이 함수가  두개의 변수의 합을 반환한다고 말한다. 
자바 8에서는, Integer는 정적 메서드(sum)을 지원하는데 완전 똑같은 역활을 한다. 

그래서 우리는 간단히 
map.merge(key, 1, Integer::sum);을 하면 된다.

람다를 통해서 할수 없는 거라면 method reference를 통해서도 불가능하다. 
그리고 이것은 method reference가 더 짧고, 깔끔한 코드라는 걸 보통 말한다. 

만약 IDE를 쓰면, 람다를 method reference로 대체할수 있는 기능이 있는데. 보통은 하는게 좋지만 항상은 아니다. 가끔은 람다를 쓰는게 더 간편할때가 있는 데, 메서드가 람다안에 같으 클래스에 있을때,

예를 들어, GoshThisClassNameIsHumongous 라는 클래스에 있다고 가정하자 .

service.execute(GoshThisClassNameIsHumongous::action)
람다로는 이렇게 보인다
service.execute( () -> action());
이런 식으로 보면 method refernece가 더 길고 깨끗하지도 않기 때문에 후자인 람다가 더 낫다. 

많은 method reference 정적 메서드를 참조하지만, 4가지 참조하지 않는 경우가 있다. 
두가지는 bound와 unbound 인스턴스 메서드 참조이다. 
bound 참조는 method reference에서 받는 객체를 단정짖는다. 
바운드는 정적 참조의 흐름과 매우 유사한다 : 함수 객체는 같은 변수를 참조된 메서드로서 받는다. 

언바운드 참조의 경우, 받는 객체는 함수 객체가 적용되었을때 종속된다. 메서드의 파라미터가 선언되기 전에 추가적인 파라미터를 통해서 
언바운드는 종종 스트림 파이프라인에 매핑 그리고 필터 함수에 사용된다. 
마지막으로 클래스와 배열을 위한 생성자 참조가 있다. 

생성자 참조는 팩토리 객체에 영향이 있다. 

요약하자면, methodReferences 람다에 비해 종종 훨씬 간단한 방법을 종종 제공한다. method references 는 짧고 간결하기 때문에 이걸 쓰자!  


### Favor the use of standard functional interfaces

표준 함수적 인터페이스 사용을 지지하자


42. Prefer lambdas to anonymous classes

### 45. Use streams judiciously(스트림은 신중하게 사용하자) 

자바 8에서 벌크 연산 수행 업무를 쉽게해주는 스트림 API가 추가 되었다. 두 개의 추상화가 있는데, 스트림(stream)은 유한하거나 무한한 데이터 엘레멘트의 연속이다. 스트림 파이프라인(pipe-line)은 스트림의 엘레멘트의 다단계의 연산 과정이다.

컬렉션, 배열, 다른 스트림이 스트림으로 사용된다. 
엘레멘트 데이터는 참고 객체 또는 int, long and double을 지원한다. 

스트림 파이프라인은 원형 스트림으로 구성되는데, 원형 스트림은 0 또는 중간 연산 그리고 터미널 연산으로 만들어 진다. 

중간 연산(intermediate operation) : 스트림을 여러가지 방법으로 다른 스트림으로 변환 한다. 

종료 연산(terminal operation) : 마지막 중간 연산으로 부터 스트림의 결과를 최종 계산한다. 


스트림 파이프라인의 특징

- lazily
-- 계산을 종료 연산이 실행되기 전까지 수행하지 않는다. 그리고 종료 연산을 완성하기 위해 불필요한 데이터 엘레멘트는 절대 계산 되지 않는다. 이렇게 함으로서 유한한 스트림에서 수행이 가능하다. 종료 연산은 반드시 넣어야 스트림 파이프라인은 동작한다. 꼭 넣자
- fluent
-- 모든 호출을 다루도록 디자인 되었는데, 한 파이프라인을 한 표현식으로 연결이 되도록 

기본적으로, 스트림 파이프라인은 순차적으로 동작한다. 병렬 환경에서 파이프 라인을 수행하게 하는건 스트림에 있는 parallel 메서드를 사용하면 된다. 그러나 드물게도 적절하지 않은 경우도 있다. 

스트림 API는 스트림을 사용해서 실용적으로 어떤 연산도 가능하기 때문에 굉장히 다재다능(versatile)하다. 그렇다고 해서 꼭 스트림 API를 쓰라는 말은 아니다. 
적절히 쓰면, 코드를 짧고 명료하게 만들수 있고, 나쁘게 쓰면, 프로그램을 읽고 유지보수하게 어렵게 한다. 언제 스트림을 사용할지 확고한 규칙은 없고 시행착오를 겉쳐야 한다(heuristics).

anagrams : 두 단어가 다른 순서지만 같은 알파벳으로 이루어져 있다. 

// prints all large anagram groups in a dictionary iteratively
```
Map<String, Set<String>> groups = new HashMap<>();
...
while(s.hasNext()){
	String word = s.next();
	groups.computeIfAbsent(alphabetized(word), (unused) -> new TreeSet<>()).add(word);
}
```
**groups.computeIfAbsent(alphabetized(word), (unused) -> new TreeSet<>()).add(word);**

볼드 처리가 되고 각 단어를 맵에 넣는 코드는 공부할 가치가 있다. computeIfAbsent메서드는 맵의 키를 먼저 보고 만약 키가 존재하면 연관되는 값을 반환한다. 맵의 키가 없다면, 메서드는  주어진 키를 함수 객체에 대입하여 계산된 값을 반환한다. 
즉 키가 없으면, 새로운 TreeSet을 만들고  word를 해당  TreeSet에 추가한다. 그리고 이 TreeSet은 해당 키로 매핑된다. 

그런데 같은 문제를 푸는데 스트림을 엄청 많이 써서 한다면 ...

물론 더 짧기는 하지만 읽을 수가 없다. 스트림의 과도한 사용은 프로그램을 읽고 유지하기 힘들게 한다. 
다행이 중간 타협점이 있다. 

```
words.collect(groupingBy(word -> alphabetized(word)))
.values().stream().
.filter(group -> group.size() >= minGroupSize)
.forEach(g -> System.out.println(g.size() + ":" + g);
```
위의 스트림에서 파이프라인은 중간 연산이 없다. 종료 연산자는 모든 단어를 맵으로 모은다. 

주의할 점은 람다 파라미터의 이름을 조심해서 골라라. 파라미터 g는 group이라고 하는게 맞는데 코드 라인이 너무 길어서 ... 
만약 정확한 타입을 모르겠다면, 스트림 파이프라인의 가독성을 위해 주의 깊은 람다 파라미터의 내이밍은 필수적이다. 

자바는 primitive char를 사용하는 스트림을 잘 지원해주지 않는다.( 그렇다고 자바가 char 스트림을 반드시 지원해야한 다는것은 아니고 사실 실행 불가능하게 하는게 맞다)
char 스트림의 해악을 설명하자면 아래 코드를 보자 :  
```
"Hello".chars().forEach(System.out::print);
```
그런데 위를 실행해보면 hello가 안나오고 이상한 정수값이 출력이 된다.


"Hello".chars()를 실행해보면 사실 int values를 반환한다. 
chars() 메서드가 int 값들의 스트림을 반환하는건 솔직히 혼란스럽다.
이걸 피할려면 강제 형변환을 통해서 고쳐야 한다. 
```
"Hello".chars().forEach(x -> System.out.print((char)x));
```
 그러나 이상적으로 char 값을 다루는데 스트림을 사용하는것을 지양하는게 좋다.

스트림 사용을 시작할때, 모든 루프를 스트림으로 변환하고 싶은 충동이 생길지 모르겠다.
그러나 그러한 충동은 참아라. 어쩌면 기존 코드의 유지성과 가독성을 해칠지도 모른다. 
규칙상, 어렵고 복잡한 업무는 스트림과 이터레이션의 조합을 사용하는 것인 최고다. 따라서, 스트림과 이터레이션의 조합으로 가는 방향에 맞는 경우라면, 존재하는 코드를 스트림을 사용하고 새로운 코드에서 사용하게 리팩토링 하자


스트림 파이프 라인은 반복적인 계산을 함수 객체로 표현한다. 그에 반해서 일반적인 이터레이티브 코드는 코드 블락을 사용해서 표현한다. 
아래 내용은 코드 블럭에서는 가능하지만 함수객체에서는 할수 없는 것들이다. 

* 코드블럭에서는 스코프 안에 어떤 로컬 변수는 읽거나 수정이 가능하다; 람다에서는, 단지 마지막 변수들을 읽을수만 있다. 그리고 어떤 로컬 변수도 수정이 불가하다. 
* 코드블럭에서는  감싸는 메서드 안에서 return이 가능하고 그밖에, break or continue가 가능하다. 또는 throw 로 execption 처리도 가능하다. 그러나 람다에서는 이것이 불가능 하다. 

만약 위에 기술을 사용하는것이 최고의 표현이라면, 스트림을 쓰는 것은 적합하지 않다. 반대로 말하자면, 스트림은 아래 상황에서 스기 좋다.

* 일괄적으로 엘레멘트의 연속을 변경할때 
* 엘레멘트를 필터 할때
* 간단한 오퍼레이션을 사용해서 결합할때
* 엘레멘트들을 누적하여 컬렉션을 만들때, 몇명 속성으로 그룹핑을 할때
* 특정 기준을 만족하는 엘레멘트를 엘레멘트들에서 찾을때

언제 스트림을 쓸지 이터레이션을 쓸지 불명확한 경우가 엄청 많다.
프로그래밍 화는 환경과 개인적인 선호에 따라 다른거 같다. 
이터레이션을 사용하면 심플하고 더 자연스럽게 보일지도 모른다. 그리고 많은 수의 자바 프로그래머가 쉽게 이해하고 유지보수하기 좋다. 그러나 몇몇 프로그램은 스트림을 쓰는게 좋다고 생각 한다.

만약 어떤 것을 써야할지 모르겠다면, 이터레이션으로 하는게 안전하다. 

요약하자면, 몇몇 작업은 스트림에 적합하지만 어떤건 이터레이션이 좋다. 많은 작업은 두 가비버의 결합으로 처리하는게 최고의 발행이다. 정 햇갈리면 둘다 구현해보고 어떻게 더 나은지 판별해라

### 46. Prefer side-effect-free functions in streams(스트림에 사이드 이펙트가 없는 함수를 사용하자)

만약 처음 스트림을 접했다면, 그것들을 쓰기 어려울수 있다. 
그리고 용캐 성공해도 거의 이익을 느끼지 못할 수도 있다. 스트림은 단순히 한 API가 아니다. 이것은 함수적 프로그래밍에 기반한 패러다임이다. 표현력, 속도 그리고 스트림이 제공하는 병행성을 얻기 위해, 반드시 API 뿐만 아니라 패러다임에 받아 들여야 한다. 

스트림 패러다임의 가장 중요한 부분은 일련의 변형으로 계산을 만들어 가는것인데, 전 스태이지에서 결과의 순수한 함수로 최대한 근접하게 각 스태이지의 결과다.?

순수한 함수는 결과가 오직 이것의 입력에 의지하는 것이다. 이것은 어떤 변경이 있는 상태나, 어떤 상태를 갱신하지 않는다. 이런 순수한 함수를 얻기 위해, 스트림 연산에서 사용하는 모든 함수적 객체는 중간 연산자, 터미널 연산자이고, side-effects로 부터 자유롭다. 

```
// Uses the streams API but not the paradigm -- Don
't do this!

Map freq = new HashMap();
try (Stream<String> words = new Scanner(file).toekns()){
	words.forEach(word -> {freq.merge(word.toLoweCase(), 1L, Long::sum)}
}
```
일단 스트림을, 람다, 그리고 메서드 참조를 모두 사용했고 올바른 답을 구했다. 줄이자면, 이것은 스트림 코드가 전혀 아니다. 이것은 겉치레(masquerading)만 스트림 코드인 이터레이티브 코드다. 
이것은 스트림 API를 사용함으로써 어떤 이득도 없다, 그리고 이것은 약간 더 길고, 읽기 불편하고 , 이터레이티브 코드에 비해 유지성이 떨어진다.  문제는 forEach  람다를 사용하여 외부 상태(the frequency table)을 변경하는 연산을 통해 모든 일을 처리하는게 이 코드의 문제다. 
스트림으로 수행한 연산의 결과를 나타내는것 말고는 역할이 없는 이 forEach 연산은 코드상에서 악취가 난다. 또한 람다는 상태를 변경한다.  그러면 위의 코드를 어떠헥 코치는게 좋을까?

```
// Proper use of streams to initalize a frequency table

Map freq = new HashMap();
try (Stream<String> words = new Scanner(file).toekns()){
	freq = words.collect(groupingBy(String::toLowerCase, counting()));
}
```

스트림에 있는 forEach 터미널 연산자는 터미널 연산자 중에서 가장 약하고 가장 스트림에 맞지 않는다. 완전히 iterative하고 그래서 병행 작업에 수정이 불가능하다.

**The forEach operation should be used only to report the result of a stream computataion, not the perform the computation.**
  
  forEach 연산자는 연산 수행이 아닌, 스트림 연산의 결과를 기록하는 용도로만 사용해야 한다. 
  때때로 몇몇 다른 목적으로 forEach를 사용하는것도 문맥에 맞다. 예를 들어 이미 이전에 존재하는 컬렉션에 스트림 연산의 결과를 추가하는 경우와 같을때.

컬렉터의 경우, 스트림에서 사용하기 위해 새로운 컨셉을 배워야 한다. 
컬렉터 API는 협박을 한다;  이건 39의 메서드를 가지고, 몇몇은 5개의 파라미터를 가진다. 좋은 소식은 이러한 복잡성을 (손으로 더듬다, 깊게 파헤치다)delving 깊게 파헤칠 필요 없이 이 API로 부터 이익을 얻을 수 있다. 초보자에게, 컬렉터 인터페이스를 무시하고 컬렉터를 리덕션 전략을 캡슐화하는 투명한 객체라고 생각 할수 있다. 여기서 말하는 리덕션(reduction)은 스트림의 엘레멘트들을 하나의 단일 객체로 결합하는것을 말한다.  전형적으로, 컬렉터의 이해 만들어진 객체는 하나의 컬렉션이다. (컬렉터는 하나의 컬렉션을 만든다. )

스트림의 엘리멘트들을 모아 컬렉션으로 만드는 컬렉터는 직관적이다. 이러한 컬렉터에는 새 가지 종류가 있다: toList(), toSet() 그리고 toCollection (collectionFactory).  이것들 각각은 셋, 리스트 그리고 프로그래머가 정의한 컬렉션 타입을 반환한다.  위에서 만든 빈도수 테이블을에서 top-ten 리스트를 추출할 수 있다. 

```
//Pipeline to get a top-ten list of words from a frequency table
List<String> topTen = freq.keySet().stream()
	.sorted(comparing(freq::get).reserved())
	.limit(10)
	.collect(toList));
```

주목할 점은, 우리는 아직 toList 메서드에 이것의 클래스인 컬렉터를 명시 하지 않았다. 습관적으로 그리고 컬렉터들의 모든 멤버를 정적으로 import하는것은 현명한데 왜냐하면, 이것이 스트림 파이프 라인을 더 읽기 쉽게 만들기 때문이다. 

이 코드의 유일하게 트키이 한 부분은 비교자하는 부분이다. 
comparing 메서드는 키 추출 함수를 가지는 comparator의 생성자 메서드이다. 
그 함수는 단어를 받고 '추출'은 사실 테이블을 본다 :  안에 들어있는 method reference인 freq::get 은  빈도 테이블에 단어를 보고 파일에 나타난 단어들의 횟수를 반환한다. 마침내, 우리는 reserved메서드를 하고 가장 빈도수가 큰것으로부터 작은 것으로 단어들을 정렬한다. 그리고 스트림에서 10 단어를 잘라 리스트로 만든다. 

코드상에서 Scanner's stream 메서드를 써서 스캐너로 부터 스트림을 얻었다. 
이 메서드는 자바 9에서 추가 되었다. 만약 이것보다 빠른 버전에서 사용한다면, streamOf(Iterable<E>)를 사용하여 스캐너를 스트림으로 변환 가능하다. 

그렇다면 컬렉터들 안에 있는 다른 36개의 다른 메서드는 어떨까? 그들 대다수는 스트림을 맵으로 만들기 위해 존재하는데, 그것들을 진짜 컬렉션으로 만드는 것보다 훨씬 복잡하다. 각 스트림 엘레멘트는 키와 값으로 짝지어 진다 그리고 멀티플 스트림 엘레멘트는 같은 키로 짝지어진다. 

가장 간단한 맵 컬렉터는 toMap(keyMapper, valueMapper), 두개의 함수 객체를 받는데, 하나는 스트림 엘리멘트를 하나의 키로 짝짓고, 나머지 하나는 스트림 엘리멘트를 하나의 값으로 짝짓는다. 

```
// Using a toMap collector to make a map from string to enum
private static fianl Map<String, Operation> stringToEnum = Stream.of(values()).collect(toMap(Object::toString, e -> e));
```

스트림의 각 엘리멘트가 유니크 키값을 가지면 완벽하다.  **다수의 엘리멘트가 같은 키에 매칭이 되면, 파이프 라인은 IllegalStateException과 함께 실패할것이다.** 

groupingby 메서드 뿐만 아니라 toMap의 더 복잡한 형태들은 이러한 충돌을 다루기 위해 다양한 전략을 제공한다. 
한 방법은 toMap 메서드에 merge 함수를 키 매퍼아 값매퍼에 제공하는 것이다. 
merge 함수는 BinaryOperator<V>, V는 맵의 값의 타입이다. merge 함수를 사용하면 하나의 키에 대응하는 어떤 추가적인 값도 존재하는 값과 결합한다. 예를들어,  merge 함수가 곱하기라면, 한 키와 대응하는 최종 결과 값은 그 키에 대응하는 값들의 모든 곱하기이다. 

toMap의 세번째 알규먼트는 또한, 키와 선택된 엘리멘트의 맵을 만드는데 유용하다. 예를 들어, 다양한 아티스트들의 앨범 기록의 스트림이 있고, 우리가 기록 아티스트로부터 best-selling 앨범 맵을 만들고 싶다고 하자. 

다수의 앨범이 들어오고, 최종적으로 맵에는 그 아티스트의 앨범중 가장 판매량이 높은 앨범정보가 들어간다. 
```
// Collector to generate a map from key to chosen element for key 
Map<Artist, Album> topHits = albums.collect(
toMap(Album::artist, a->a, maxBy(comparing(Album::sales))));
```

maxBy는 정적 팩토리 메서드이고 BinaryOperator로 부터 정적으로 임포트 되었다. 

toMap 메서드의 세번째 아규먼트의 또 다른 사용법은 컬렉터간의 충돌이 있을 경우,  마지막-쓰기-승리 정책을 포함하는 컬렉터를 생산하게 한다. 
많은 스트림에게, 이러한 결과는 비결졍적이다, 그러나 매핑 함수에 의해서 연관되어진 모든 값이 같거나, 모든것이 받아들일법 하면, 컬렉터들의 행동은 너가 원하는 것일지도 모른다. 

```
// Collector to impose last-write-wins policy
toMap(keyMapper, valueMapper, (v1, v2) -> v2)
```
toMap의 세번째 그리고 네번째 버전은 4번째 아규먼트를 받는다. 
이것은 EnumMap or TreeMap과 같은 특별한 맵을 정하고 싶을때 사용한다. 

toMap에는 추가적으로 groupingBy 메서드가 존재하며, classifier 함수에 기반하여 그룹 엘리멘를 카테고리에 대응하는 맵을 생상한다. classifier 함수는 엘리먼트를 받아 맞아 떨어지는 카테고리를 반환한다. 이 카테고리는 엘리멘트의 맵 키처럼 활동한다. 
가장 간단한 groupingBy 메서드는 classifier만 받고, 각 카테고리에는 대응하는 엘레멘트들의 리스트를 가지는 맵을 반환한다. 

만약 니가 카테고리에 대응하는 값이 리스트가 아닌 맵을 원하면, classifier에 downstream 컬렉터를 추가 정의가 가능하다. 
downstream 컬렉터는 한 카테고리에서 모든 엘리멘트를 포함하는 스트림으로부터  값을 생성한다. 가장 간단한 파라미터 사용법은 toSet()을 쓰는것인데 리스트가 아니라 엘레멘트들의 셋을 값으로 가지는 맵을 결과로 가져온다. 
대안으로, toCollection을 사용할 수 있는데, 각 카테고리의 엘리멘트들이 있는 곳에 컬렉션을 만들어 넣을 수 있다.  이것은 너가 원하는 어떤 컬렉션 타입이라도  선택할수 있도록 유연하다.  groupingBy의 두번째 알규먼트를 쓰는 방법은 count()을 downstream으로 쓰는 것이다.  이것의 결과는 엘리먼트들을 가지는 컬렉션이 아니라 각 카테고리에 해당하는 엘리먼트의 갯수를 각 카테고리에 매핑하는 맵을 반환한다. 

groupingBy의 세번째 버전은 downstream 컬렉터에 맵 팩토리를 추가하는 것이다. 주목할점은 이 메서드는 표준 리스트 패턴의 텔레스코핑 알규먼트를 위반한다. : mapFactory 파라미터는 선행하는 점, 따르기 보다는, 다운 스트림 파라미터를, 
그래서 너는 값이 TreeSet인 TreeMap을 반환하는 컬렉터를 정의할 수 있다.

groupingByConcurrent 메서드는 groupingBy의 모든 세가지를 다루는 다양성을 제공한다. 이것은 병렬적으로 효과적으로 동작하고 ConcurrentHashMap 객체를 반환한다. 그리고 드물게  partitioningBy라고 하는 groupingBy의 대체를 사용할때가 있다. classifier 메서드 대신에,  이것은 서술어를 가지고 키가 boolean 타입인 맵을 반환한다. 

카운팅 메서드로 반환되는 컬렉터는 downstream의 컬렉터들로 오직 사용되도록 하는 경향이 있다. 같은 함수적인 기능이 stream의 count()메서드를 통해서 사용이 가능하기 때문에, collect(counting))을 말하는 이유는 불필요 하다. 

그 외에 15가지의 컬렉터 메서드가 더 존재 한다.  그 중 9가지 메서드는 summing, averaging 그리고 summarizing으로 메서드가 시작한다. 

대다수의 프로그래머는 이러한 메서드들의 대부분을 안전하게 무시해도 된다. 디자인의 관점으로, 이러한 컬레터들은  다운스트림 컬렉터가 ministreams처럼 행동하기 위해서 컬렉터들의 안에 스트림들의 함수에 부분적으로 중복을 시도하는것을 나타낸다. 

남아있는 3가지 메서드가 남아 있다. 이것들은 collectors에 있지만 collection을 포함하지 않는다. minyBy와 maxBy가 그것이고, comparator 비교자를 받아, 그 배교자로 인해 결졍된 스트림의최소 또는 최대 엘레멘트를 반환한다. 

마지막 컬렉터 메서드는 joining이다. 스트링들과 같은 CharSequence 객체들의 스트림에만 작동한다.  이것은 엘레멘트들의 문자 합을 단순히 반환한다. 첫번째 알규먼트는 Charsequece 파라미터인 delimeger를 받는다. 
...

요약하자면, 스트림 파이프라인을 프로그래밍 하는것의 본질은 함수 객체들의 side-effects 로 부터 벗어나는 것이다. 이것은 스트림과 관련 객체들을 지나치는 모든 많은 함수 객체에게 적용된다. forEach 연산자는 반드시 계산 결과를 기록하기 위한 용도로만 쓰여야 한다. 
스트림을 적절하게 쓰기 위해서, 너는 컬렉터들에 대해 알아야 한다. 가장 중요한 컬렉터 팩토리들을 toList, toSet, toMpa, GroupingBy 그리고 Joining이다. 

### 47. Prefer Collection to Stream as a return type

엘레멘트의 연속을 반환하는 많은 메서드가 있다. 자바 8 이전에는, 명백히 이런 메서드들은 Collection,Set 그리고 List 같은 Iterable 타입을 반환했다. 
만약 메서드가 for-each 문을 가능하게 하게 위해 존재하거나 반환 값이 collection메서드를  구현할수 없다면, Iterable이 사용되었다. 
만약 반환되는 엘레멘트들이 프리미티브 값 또는 스트링을 요구하는 필요가 있다면, arrays가 사용된다.  자바 8에선, 스트림이 추가 되어, 실직적으로 적당한 반환값을 고르는게 일이 되었다. 

스트림은 엘레멘트의 연속을 반환하는데 사용하는게 맞다라는 말을 들었을지도 모른다. 그러나 45챕터에서 논의되었듯이, 스트림은 iteration을 쇠퇴하게 만들지 않는다. 좋은 코드를 만드는 것은 스트림와 이터레이션을 둘다 결합하는것을 필요로 한다. 
만약 API가 오직 스트림만을 반환하고 몇몇 사용자가 for-each 루프로 반환되는 엘레멘트를 원한다면, 이런 사용자는 매우 화가 날것이다. 
스트림 인터페이스는 iterable 인터페이스의 독립적인 추상화를 포함하고 이러한 메서드를 위한 스트림의 스펙은 Iterable의 것과 잘 어울리기 때문이다. 프로그래머로 하여금 for-each 루프를 사용하고 스트림을 통한 iterate를 쓰지않게 하는 유일한 이유는 Iterable을 확장한 스트림의 실패다. 

슬프게도, 이 문제에 대한 좋은 차선책은 없다. 처음 살펴볼때, method reference를 스트림의 iterator 메서드에 보내는것은 동작한다는 것을 이미 나타 났다. 아래 결과 코드는 아마도 지저분하고 불명확하나, 이유가 없지는 않다. 

```
 //Won't compile, due to limitations on Java's type inference
 for(ProcessHandle ph : ProcessHandle.allProcesses()::iterator){
	//process the process
}
```

이거를 컴파일 하려고 하면, 에러가 발생한다. 
 TEST.java6: error: method reference not expected here
 
 코드 컴파일을 하려면, method reference를 적당한 Iterable로 캐스트를 해야 한다. 
```
	//Hideous workaround to iterate over a stream
	for(ProcessHandle ph : (Iterable<ProcessHandle>) ProcessHandle.appProcesses():iterator)
```

위에 코드는 동작하지만, 실전에서 사용하기에 지저분하고 불명확 하다. 더 나은 수단으로 어댑터 메서드를 사용하는것이 있다. JDK는 이러한 메서드를 제공하지 않지만, 위에 코드에서 사용된 같은 테크닉을 사용해서 간편하게 쓸 수 있다. 
주목할것은 어댑터 메서드에 캐스트는 필수적이지 않다. 왜냐하면 자바의 타입 추론은 적절히 아래 문맥에 맞게 동작한다. 

``` 
	//Adapter from Stream<E> to Iterable<E>
	public static <E> Iterable<E> iterableOf(Stream<E> stream) {
	//Process the process
	}
```

위의 어댑터 메서드를 사용해서, 어떤 스트림이라도 for-each 문으로 반복해서 사용이 가능하다.  

item 34에서 Anagrams 프로그램 버전의 스트림은 Files.lines 메서드를 사용했다 사전을 읽기 위해, 반멱에 iterative 버전은 scanner를 사용했다. 
Files.lines 메서드는 스캐너의 상위 메서드로 파일을 읽는 와중에 발생하는 어떤 예외도 조용히 삼킨다. 똑같이  우리는 iterative 버전에서도 Files.lines를 사용해오곤 했다.  만약 한 API가 오직 스트림을 통한 접근만 제공하고 for-each 문과 같은 반복을 하고 싶다면, 프로그래머는 일종의 타협으로 이것을 만들것이다. 

반대로, 스트림 파이프라인을 활용하여 접근하고 싶은 프로그래머는 Iterable만을 제공하는 API를 보고 화가 날것이다. 다시 말해서, JDK는 어댑터를 제공하지 않는다. 따라서 아래와 같이 어댑터를 만들 수 있다. 

```
	//Adapter from Iterable<E> to Stream<E>
	public static <E> Strea<E> streamOf(Iterable<E> iterable) {
		return StreamSupport.stream(iterable.spliterator(), false);
}
```

만약 오브젝트들을 반환하는 메서드를 만들고 있고 스트림 파이프 라인으로만 사용된다는것을 알고 있다면, 그러면 물론 스트림을 반환하면 된다, 
비슷하게, iteration만으로만 사용된다는 것을 안다면, Iterbale을 반환하면 된다. 
대다수의 사용자가 같은 매커니즘을 사용하기 바란다는것에 믿는데 합리적인 이유를 가지고 있지 않다면, 엘레멘트들을 반환하는 public API를 만들때, 스트림 파이프라인 뿐만 아니라 for-each문을 사용하기 위한 모든 사용자를 위해 API를 만들어야 한다. 

컬렉션 인터페이스는 Iterable의 하위 타입이고 스트림 메서드를 가진다. 정리하면, 컬렉션 인터페이스는 iteration과 스트림 접근 모두를 제공한다. 그러므로, **Collection or an appropirate subtype is generally the best return type for a public, sequence-returning method.**

public 복수의 엘레멘트를 반환하는 메서드를 위해 컬렉션 또는 적절한 하위 타입은 보통 최고의 리턴 타입이다. 배열은 또한 간단한 iteration과 스트림 접근을 Arrays.asList와 Stream.of 메서드를 통해 제공한다. 메모리만으로도 충분한 엘레멘트들을 반환한다면, ArrayList 또는 HashSet과 같은 하나의 표준 컬렉션을 반환하는게 아마 최고일 것이다. 
But do not store a large sequence in memory just to return it as a collection
그러나 큰 사이즈의 엘레멘트들을 메모리에 담지마라, 단지 하나의 컬렉션을 반환하기 위해서 ! 

만약 반환하려는 엘레멘트가 크지만, 간소하게 표현될수 있다면, 특별한-목적에 맞는 컬렉션을 구현하는것을 고려해봐라.  예를들어, 주어진 셋에 대해 power set를 반환하는 것을 원한다고 가정하자. power set은 주어진 셋에 대한 모든 부분집합으로 구성되어 있다. 
만약 주어진 셋에 N개의 엘레멘트로 구성이 된다면, power set의 갯수는 2^n개 이다. 그러므로 표준 컬렉션 구현으로 파워셋을 저장하는 것을 고려할 필요는 없다.  그러나 AbstractList의 도움으로 커스텀 컬렉션 작성 작업을 쉽게 할 수 있다. 

 트릭은 power set의 각 엘레멘트들의 인덱스를 bit 벡터로 사용하는 것이다. 
인덱스의 n번째 bit는 원래 셋으로부터, n번째 엘레멘트의 존재 또는 부재를 나타낸다. 필수적으로, 0에서 2^n-1까지 이진 숫자와 사이에  n개 엘리먼트 셋의 power set사이에 자연스러운 매핑이 있다. 여기 코드가 있다.

-- 코드는 생략

주목할 점은 30개 이상의 엘레멘트를 갖는 입력이라면 예외를 뱉는다. Stream 또는 Iterable이 아닌 Collection을 반환 타입으로 사용하는 것의 단점이다. Collection은 int 타입의 반환 크기를 가지는데, 반환되는 크기를 Integer로 제한한다. 
MAX_VALUE, 또는 2^31 -1. 

컬렉션의 스펙은 2^31 -1 보다 크거나, 무한할 지라도 2^31-1을 반환하도록 허용한다. 그러나 이것이 와벽히 솔루션을 만족하진 않는다. 

정상의 AbstractCollection을 구현하기 위해 한 컬렉션을 작성하기 위해, Iterable의 concatins 그리고 size  두 가지 메서드만 구현하면 된다. 종종 이러한 메서드의 효과적인 구현을 작성하는 것은 쉽다. 만약 실형 가능하지 않다면,  아마도 그것은  엘레멘트들의 컨텐츠가 미리 결정되어 있지 않기 때문인데, iteration이 자리를 잡기 전에, 스트림 또는 iterable을 반환하는, 그리고 그러한 점은 훨씬 자연스럽다. 

만약 니가 선택한다면, 두개의 나눠진 메서드를 둘다 반환 할 수 있다. 

가끔씩, 구현을 쉽게 하기 위해 반환 타입을 선택하는 경우도 있다. 예를 들어, 입력 리스트의 부분 리스트를 반환하는 메서드를 만들고 싶다고 가정한다면, 3줄이면 코드상으로 가능하다. 부분 리스트를 만들고 그것을 표준 컬렉션에 넣으면 된다. 하지만 원천 리시트의 사이즈로부터 quadratic하게 메모리를 잡아먹는다. 이것은 물론 powerset의 exponential보다는 낫지만 명백히 받아 들일 수 없다. 

커스텀 컬렉션을 구현하는 것은, 우리가 power set을 만든것 처럼, 긴장된다. 왜냐하면 JDK은 Iterator의 뼈대를 구축하는데는 부족하다. 

prefix  : 입력된 리시트의 젤 앞에 있는 원소를 가지고 있는 리스트를 prefix List
suffix  : 입력된 리시트의 젤 뒤에 있는 원소를 가지고 있는 리스트를 suffix List
* 주의할점은 셋이 아니라 리스트이다. 

prefixList = {a}, {a,b}, {a,b,c}
suffixList = {a,b,c}, {b,c}, {c}

요약하자면, 엘레메느들의 연속을 반환하는 메서드를 작성 중일때, 그 메서드를 사용하는 사용자중 일부는 스트림으로 처리하기 원하고 반면에 다른 사람은 iterate로 사용하기 바란다. 두 그룹 모드를 해결하려고 해라. 만약 컬렉션으로 반환이 가능하다면 그렇게 해라. 
만약 컬렉션에 엘레멘트를 가지거나 컬렉션의 수가 새로운 컬렉션을 만들정도로 충분히 작다면, ArrayList와 같은 표준 컬렉션을 반환해라. 그러한 상황이 아니면 커스텀 컬렉션을 구현하는 것을 고려해라. 컬렉션을 반환하기에 실현이 불가능하다면, 언제든지 자연스뤄워 보이는 스트림 또는 iterable을 반환해라. 미래에 자바 배포에서, 스트림 인터페이스는 Iterable을 확장하기 위해 수정된다면, 그것들이 스트림 프로세스와 iteration을 모두 지원하기 때문에 스트림을 반환하면 된다. 

### 48. Use caution when making streams parallel

주요 메인 프로그래밍 언어 중에서 자바는 항상 병렬 프로그래밍 업무를 쉽게하기 위한 기능을 앞장서서 제공해왔다. 
1996년 wait/notify 스레드 메서드
자바 5에서는 concurrent library, ececutor framework 등등

병렬 프로그램을 작성하는 것은 점점 쉬워졌지만, 똑바로 사용하고 빠르게 동작하게 하는 것은 여전히 어렵다. safety 와 liveness 위반 문제는 병렬 프로그래밍가 직면한 현실이고 병렬 스트림 파이프 라인에서 예외가 없다. 

Item 45에 코드를 고려해보자.
만약 스트림 파이프라인의 parallel()을 써서 스피드 업을 해보려고 하면? 
->  CPU 사용률 90%이고 끝나지도 않아(a liveness failure)

뭐가 문제 일까? 
-> 스트림 라이브러리는 파이프라인을 어떻게 나눠야 할지 전략이 없었기 때문에 진관은 실패 했다. 

심지어 최고의 환경에서도, 스트림의 iterate 또는 중간 연산자 limit가 사용되면 하나의 파이프라인을 병렬처리 하는것은 퍼포먼스를 줄인다 위의 파이프 라인은 둘가지 이슈를 둘다 가지고 있다. 최악으로, 기본적인 병행 전략은 limit의 예측불가능함을 다루고 있다. 여기서 예측 불가능함은 필요없는 결과를 버리는 예측 또는 추가 엘레멘트를 접근함에 해로움이 없다는 예측이 불가하다는 것을 말함. 

이 이야기의 도덕성은 간단하다 : Do not parallelize stream pipelines indiscriminately. 스트림 파이프 라인들을 가리지 않고 마구 병행하지 마라.  
성능이 재난에 가까울 수도 있다.

규칙은, 스트림 병행성으로부터 성능은 ArrayList, HashMap, HashSet, 그리고 ConcurrentHashMap 객체, arrays, int ranges 그리고 long ranges에서 최고다. 
이러한 데이터 구조는 원하는 어떤 사이즈에도 맞는 subranges를 정확하고 간단하게 만들수 있어 병행 스레드간에 일을 나누기에 쉽다.  이 추상화는 스트림 라이브러리의 일을 나누기 위한 spliterator에 의해서 사용되고, 스트림과 Iterable의 spliterator 메서드에 의해서 반환된다. 
이러한 데이터 구조가 가지는 또다른 중요한 요소는 그들이 good ot execelnt 한 locality of reference를 제공한다는 점이다. locality of reference는 연속적인 엘레멘트 참조들을 메모리상에 함께 저장되는것을 말한다. 이러한 참조를 통해 참조되는 객체는 메모리에 있는 다른 객체와는 가깝지 않을지도 모른다. 즉 locality of reference를 저하 시킨다. 
locality of reference는 병행 벌크 연산에서 치명적으로 중요하다는게 밝혀졌다: 이것이 없다면, 메모리로부터 프로세서의 캐시로 데이터를 전송을 기다리느라 쓰레드들이 놀고 있는 시간이 늘어난다. 최고의 locality of reference를 가지는 것은 primitive arrays인데 왜냐하면 데이터는 메모리에 연속적으로 저장되기 때문이다. 

스트림 파이프 라인의 터미널 연산의 속성은 또한 병행 실행의 효율에 영향을 준다.
파이프 라인의 전체적인 작업과 비교해서 터미널 연산에서 많은 일들이 끝나고, 연산자 계층적으로 연속적이면, 이러한 파이프라인에 병행성 작업을 하는것은 제한적인 효과가 있을 것이다. 
병행설을 위한 최고의 터미널 연산은 reductions이다. reductions는 파이프라인으로 부터 모든 엘레멘트들을 스트림의 reudce 메서드중 하나를 사용하여 나타나게 하는것이다. 또는 미리 만들어진 reductions들은 min, max, count 그리고 sum등이 있다. short-circuiting(짧은 순회) 연산자들은 anyMatch, allMatch, 그리고 noneMatch 는 또한 병행성에 맞게 수정가능하다. 이러한 연산자들은 스트림의 collect 메서드로부터 수행되고, mutable reductions라고 불린다. mutable reductions는 병행성의 좋은 후보자는 아닌데 collections들을 모두 합치는데 드는 비용이 크기 때문이다. 

만약 너의 스트림, iterable 또는 컬렉션을 구현하고, 병행 프로그램을 하고 싶다면, 반드시 spliterator 메서드를 override를 받고 병행성의 성능을 테스트해라. 
좋은 spliterator를 작성하는것은 매우 어렵고 이책의 범주를 넘어선다. 

**Not only can parallezlizing a stream lead to poor performance, including liveness failures; it can lead to incorrect result and unpredictable behavior**

스트림 병렬화는 나쁜 성능을 이끌수 있을 뿐만 아니라 liveness failures도 보여줄수 있다. 즉 잘못된 결과 그리고 예측 불가능한 행동을 할 수 도 있다. safety failures는 그들의 스펙을 제대로 구현하지 못한 mappers, filters 그리고 다른 프로그랙머가 제공한 함수 객체를 사용하는 병렬 파이프 라인에서 발생할 수 있다. 이러한 함수 객체에 대해서 스트림 스펙은 강력한 요구를 채우고 있다. 
예를 들어, 스트림의 reduce 연산을 무시하는  accumulator와 combiner 함수 협력적이어야 하고, non-interfacing 그리고 무상태여야 한다. 니가 만약 이러한 요구를 위반하고 파이프 라인을 수행하면, 올바른 결과를 양보할지도 모른다. 

여기 라인에서는 프라임을 노출할때 오름차순으로 노출하지 않을 것이다 . 이럴땐 forEachOrdered를 사용하면 되며,  마주친 순서대로 병렬 스트림을 순회하는 것을 보장한다. 

원형 스트림을 효과적으로 자르는 병렬적이고 가벼운 터미널 연산자이고 참조를 하지 않는(non- interferening) 함수 객체를 사용한다고 가정해도, 병렬성 비용을 실제 작업에서 충분히 상쇄하지 않는한, 좋은 스피드업을 얻기는 힘들것 이다. 
아주 너프한 추정으로, 스트림의 모든 엘리먼트의 숫자는  엘리먼트당 수행되는 코드의 수보다, 최소 hundred thousand 배만큼 많아야 한다. 

스트림의 병렬처리는 엄밀하게 성능 최적화라는것을 기억해야 한다. 이것을 할만한 가치가 있는지 병령처리 전과 후에 성능을 비교해야 한다. 이성적으로 실제 시스템 환경에서 테스트를 해봐야 한다. 보통 모든 병령 스트림 파이프라인은 common fork-join pool이다. 하나의 잘못 동작한 파이프 라인은 시스템의 연관되지 않은 부분에 성능을 해칠수 있다. 

스트림을 병렬적으로 쓴다고 해서 꼭 의미가 있는 성능을 보이진 않지만, 올바른 상황에서는, 프로세스 코어의 갯수에 따라 선형에 가까운 스피드 업을 단순히 스트림 파이프 라인에 parrellel 함수를 호출하도록 하여 얻는게 가능하다. 머신 러닝이나 데이터 처리같은 분야는 특별히 이러한 스피드업을 얻기 좋다. 

요약하자면, 계산의 정합성이 보존될것이고 성능을 높일것이라는 믿음에 대한 확실한 이유가 있지 않는한, 스트림 파이프 라인의 병렬화를 쓸 시도조차 하지 마라! 
부적절한 병렬화로 인한 피해는 성능의 재난과 프로그램의 실패이다. 병렬화 아마 정당하고 코드가 올바르게 유지되는것이 확실해 보인다면, 조심스럽게 실제 환경에서 성능 측정을 해라. 너의 코드가 올바르고 이러한 실험에서 높아진 성능으로 의심이 줄어든다면, 오직 그때만 생산코드 안에서 스트림 병렬를 사용해라. 









































 
  
> Written with [StackEdit](https://stackedit.io/).
