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

논리적 동치성을 확인하도록 재정의 해두면, Map의 키와, Set의 원소로 사용할 수 있게 된다. 값 클래스라고 해도, 값이 같은 클래스가 둘 이상 만들어지지 않음을 보장하는 클래스라면 equals를 재정의 하지 않아도 된다. 이런 클래스는 당연히 논리적으로 같은 인스턴스가 2개이상 만들어지지 않으니 논리적 동치성과 객체 식별성이 같다.

### 동치 관계(equivalence relation)

 equals 메서드를 재정의할때는 반드시 일반 규약을 따라야한다. 다음은 Object명세에 적힌 규약이다. 

equals 메서드는 동치관계(equivalence relation)을 구현하며 아래 조건을 만족해야 한다. 

* 반사성(reflexivity) : null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true.
* 대칭성(symmetry) : null이 아닌 모든 참조 값 x,y에 대해 x.equals(y) == y.equals(x)를 만족한다.
* 추이성(transitivity) : null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y), y.equals(z)가 true이면 x.equals(z)도 true다.
* 일관성(consistency) : null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)를 반복해서 호출해도 항상 true이거나 false이다. 
* null-아님 : null이 아닌 모든 참조값 x에 대해, x.equls(null)은 반드시 false이다. 

수학에 익숙하지 않다면 다소 어려울수 있지만 굉장히 중요한 내용이다. 컬렉션에서는 여러 객체를 전달 받는데 equals 규약을 지킨다고 가정한다. 만약 equals를 제대로 지키지 않은 상태의 객체가 컬렉션에 들어간다면 어떻게 동작할지 예측이 어렵다.

먼저 Object 명세에서 말하는 동치 관계란 무엇일까? 쉽게 말해 **집합을 서로 같은 원소들로 이루어진 부분집합으로 나누는 연산**이다. 이 부분 집합을 동치류(equivalence class; 동치 클래스)라 한다. equls 메서드가 쓸모있으려면 모든 원소가 같은 동치류에 속한 어떤 원소와도 서로 교환할 수 있어야 한다. 

동치관계에 속하는 객체가 되려면 위에서 이야기한 다섯 요건을 만족해야 한다.  하나씩 자사히 보기로 하자. 

#### 반사성(reflexivity)

단순히 말해 객체는 자기자신과 같아야 한다. 

#### 대칭성(symmetry) 

대칭성은 두 객체는 서로 equals 여부가 같아야 한다는 뜻이다. 
```
public final class CaseInsensitiveString {
	private final String s;
	public CaseInsensitiveString(String s){
		this.s = Objects.requireNonNull(s);
	}
	@Override 
	public boolean equals(Object o){
		if( o instanceof CaseInsensitiveString){
			return s.equalsIgnoreCase( (CaseInsensitiveString)o.s )
		}
		if( o instance of String){
			return s.equalsIgnoreCase((String) o);
		}
		return false;
	}
}
```
CaseInsensitiveString의 equals 메서드를 보면 순진하게도 일반 문자열과도 비교를 시도한다. 

```
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";
cis.equals(s) == true; // Polish.equalsIgnoreCase(polish);
s.equals(cis) == false; 
```
문제는 CaseInsensitiveString의 equals는 일반 String을 알고 있지만 String의 eqauls는 CaseInsensitiveString의 존재를 모른다. 따라서 대칭성이 명백히 위반된다. 
```
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";
cis.equals(s) == true; // Polish.equalsIgnoreCase(polish);
s.equals(cis) == false; 
```
```
// CaseInsensitiveString 클래스 내부
@Override 
public boolean equals(Object o){
	return o instance of CaseInsensitiveString && 
		((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
	// 
}
```
위처럼 String을 처리는 제외하고 같은 CaseInsensitiveString  일때만 서로 내부에 String s 필드간 equalsIgnoreCase 메서드를 실행한다. 

#### 추이성(transitivity) 

추이성은 첫 번째 객체와 두 번째 겍체가 같고, 두 번째 겍체와 세 번째 객체가 같으면, 첫 번째 객체와 세 번째 객체도 같아야 한다는 뜻이다. 간단해 보이지만 자칫하면 어기기 쉽다. 상위 클래스에 없는 새로운 필드를 하위 클래스에 추가하는 상황을 생각해보자. equals 비교에 영향을 주는 정보를 추가한 것이다.  예를 들어 2차워 점을 나타내는 Point 클래스로 생각해보자.

```
public class Point {
	@Override 
	public boolean equals(Object o){
		if(!(o instanceof Point)){
			return false;
		}
		Point p = (Point) o
		return p.x == x && p.y == y;
	}
}
```
```
public class ColorPoint extends Point {
	private final Color color;
	public ColorPoint(int x, int y, Color color){
		super(x,y);
		this.color = color;
	}
	...
}
```
여기서 ColorPoint의 equals는 어떻게 하는게 좋을까? 그대로 두면 Point의 equals가 그대로 사용되어 Color 정보를 무시한채로 비교를 수행한다. 따라서 그것은 용납이 안된다. 아래와 같은 equals메서드를 만들었다고 해보자
```
@Override public boolean equals(Object o){
	if(!(o instanceof ColorPoint)){
		return false;
	}
	return super.equals(o) && ((ColorPoint) o).color == color;
}
```

```
Point p = new Point(1,2);
ColorPoint cp = new ColorPoint(1,2, Color.RED);
p.equals(cp) == true // cp instanceof Point == true; 
// ColorPoint는 Point의 하위 클래스로 Point로 형변환이 가능하다.
cp.equals(p) == false // p instanceof ColorPoint == false;
// Point는 ColorPoint의 상위 클래스로 ColorPoint로 형변환이 불가하다.
```
위의 코드는 대칭성이 깨진다. 그러면 ColorPoint equals가 Point와 비교할때는 색상을 무시하도록 하면 해결될까?

``` 
@Override public boolean equals(Object o){ //ColorPoint
	if(!(o instanceof Point)){
		return false;
	}
	if(!(o instanceof ColorPoint)){ // Point클래스지만 ColorPoint가 아닌경우
		return o.equals(this); // 색상을 무시하고 o가 가지는 equals로 판단
	}
	return super.equals(o) && ((ColorPoint) o).color == color;
}
```
```
ColorPoint p1 = new ColorPoint(1,2, Color.RED);
Point p2 = new ColorPoint(1,2);
ColorPoint p3 = new ColorPoint(1,2, Color.BLUE);
p1.equals(p2) == p2.equals(p3) == true;
p1.equals(p3) == false;
```
위의 코드는 전이성이 미성립함을 보인다. 위의 예제에서 보았듯이 다양한 경우에서 동치 요건이 맞지 않는 경우가 발생할 수 있다. 그렇다면 근본적인 해법은 무엇일까? **사실 이 현상은 모든 객체 지향 언어의 동치 관계에서 나타나는 근본적인 문제다.**

**구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.**   객체 지향적 추상화의 이점을 포기하지 않는 한 말이다. 

이 말은 얼핏, instanceof 검사를 getClass 검사로 바꾸면 규약도 지키고 값도 추가하면서 구체 클래스를 상속할 수 있다는 뜻으로 보인다. 

```
@Override public boolean equals(Object o){ 
	if( o == null || o.getClass() != getClass())
		return false;
	Point p = (Point) o;
 	return p.x == x && p.y == y;
}
```
instaceof 대신 getClass를 쓰면 완전히 같은 클래스 객체와 비교할때만 true 값이 나올 수 있다. 하지만 실제로 활용할 수는 없다. Point의 하위클래스는 여전히 Point이므로 어디서는 Point로써 활용되어야 한다. 이 말을 유식하게 리스코프 치환원칙을 말한다. 
**리스코프 치환 원칙(Liskov substitution principle)은 어떤 타입에 있어 중요한 속성이라면 그 하위타입에서도 마찬가지로 중요하다. 따라서 그 타입의 모든 메서드가 하위 타입에서도 똑같이 잘 동작해야 한다.**

구체 클래스의 하위 클래스에서 값을 추가할 방법은 없지만 괜찮은 우회 방법이 하나 있다. 
바로 상속 대신 컴포지션을 사용하는 방법이다. Point를 상속하는 대신 Point를 ColorPoint의 private 필드로 두고, ColorPoint와 같은 위치의 일반 Point를 반환하는 뷰 메서드를 public으로 추가하는 식이다. **기존 클래스가 새로운 클래스의 구성요소로 쓰인다**는 뜻에서 이를 **컴포지션(Composition)**이라고 한다.

여기서 주의할 점은 컴포지션(Composition)과 컴포짓 패턴(Composite Pattern)은 아무 연관이 없다. 단순히 컴포지션은 한 객체가 다른 객체를 포함하고 있는 현상을 말한다. 

```
public class ColorPoint {
	private final Point point;
	private final Color color;
	public ColorPoint(int x, int y, Color color){
		point = new Point(x,y);
		this.color = Objects.requiredNonNull(color);
	}
	...
	public Point asPoint() {
		return point;
	}
	@Override 
	public boolean equals(Object o){
		if(!(o instanceof ColorPoint)){ 
			return false;
		}
		ColorPoint cp = (ColorPoint) o; // o는 ColorPoint를 포함한 하위 클래스가 될수 있다. 
		return cp.point.equals(point) && cp.color.equals(color); 		
	}
}
```

#### 일관성(consistency) 
일관성은 두 객체가 같은 상황이라면 ( 어느 한 객체 또는 두 객체 모두가 수정되지 않는한) 앞으로도 영원히 같아야 한다는 점이다. 가변 객체는 비교 시점에 따라 달라질수도 있지만, 불변 객체는 한번 다르면 영원히 달라야 한다. 클래스를 작성할때 불변 클래스로 만들기로 했다면 equals 결과는 영원히 같아야 한다. 

클래스가 불변이든 가변이든 equals 판단에 신뢰할 수 없는 자원이 끼어들게 해선 안된다. 이 제약을 어기면 일관성 만족이 매우 어렵다. 이런 문제를 피하려면 equals는 항시 메모리에 존재하는 객체만을 사용한 결정적(deterministic) 계산만 수행해야 한다. 

#### null-아님

null-아님은 이름처럼 도느 객체가 null과는 값지 않아야 한다. 의도하지 않았음에도 equals(null)이 true가 반환되는 상황은 상상하기 어렵지만, 실수로 NPE을 던지는 코드는 흔할것이다. 따라서 아래와 같이 명시하는 방법이 있다.
```
@Override 
public boolean equals(Object o){
	if(o == null){ 
		return false;
	}
```
하지만 이런 검사는 불필요하다고 한다. instanceof 비교를 null 객체로 하면 무조건 false가 반환되기 때문이다. 
```
@Override 
public boolean equals(Object o){
	if(!(o instanceof MyType)){  //o가 null이면 무조건 false가 된다.
		return false;
	}
```

### 양질의 equals 메서드 구현 

1. == 연산자를 사용해서 입력이 자기 참조인지 확인한다. 단순한 성능 최적화용으로, 비교 작업이 복잡하다면 equals 과정을 거친다.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다. 
3. 입력을 올바른 타입으로 형변환한다. 2번에서 instanceof로 검사했기 때문에 반드시 성공한다. 
4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사한다. 모든 필드가 일치하면 true, 그외라면 false이다.


기본적으로 float과 double을 제외한 모든 기본 타입 필드는 ==로 비교하고 참조타입은 equals로 비교한다. float과 double는 Float.compare(), Double.compare()로 비교한다. float과 double을 특별 취급하는 이유는 Float.Nan, -0.0f와 같은 특수한 부동소수 값을 다뤄야 하기 때문이다. Float.equals(), Double.equals()를 써도 되지만 오토박싱이 될수도 있어 성능상 안좋을 수 있다. 
배열의 경우 모든 원소를 하나씩 비교해 봐야한다면 Arrays.equals 메서드를 쓰도록 하자. 

어떤 필드를 먼저 비교하느냐에 따라 equals의 성능이 달라질 수 있다. 이왕이면 가능성이 더 크거나 비용이 싼 필드를 먼저 비교하자. 

### 정리하면서 

마지막으로 equals 를 다 구현했다면 대칭적, 추이성, 일관성을 만족하는지만 자문해보자. 자문에서 끝내지 말고 실제 단위 테스트를 하자. 세 요건 중 하나라도 실패하면 원인을 찾아 고치자 나머지 요건은 사실 문제가 되는 경우가 별로 없다. 
다음은 마지막 주의사항이다

* equals를 재정의할땐 hashCode도 반드시 재정의하자.
* 너무 복잡하게 해결하지 말자. 필드의 동치성만 검사해도 규약을 어렵지 않게 지킬 수 있다. 일반적으로 필드의 별칭(alias)는 비교 대상에 넣지 말자.
* Object가 아닌 타입을 매개변수로 받는 equals 메서드는 선언하지 말자. equals 메서드는 반드시 Object이다. 만약 Object아닌 타입을 받게 만들었다면 그건 Override가 아닌 Overloading이다.

>꼭 필요한 경우가 아니라면 equals는 재정의하지 말자. 대다수 Object의 equals가 원하는 비교를 정확히 해준다. equals를 재정의 할때는 그 클래스의 핵심 필드를 빠짐없이, 다섯 요건z을 지켜가며 비교를 해야 한다. 

## Item11. equals를 재정의하려거든 hashCode도 재정의하라

반드시 equals를 재정의한 클래스 모두에서 hashCode도 재정의해야 한다. 그렇지 않으면 hashCode 일반 규약을 어기게 되어 해당 클래스 인스턴스가 HashMap이나 HashSet같은 컬렉션의 원소로 사용되면 문제가 발생할 것이다. 

아래는 Object 명세에 적혀있는 규약이다.

* equals(Object)가 두 객체가 같다고 판단했다면, 두 객체의 hashCode도 똑같은 값을 반환해야 한다
* equals(Object)가 두 객체가 같다고 다르다고 판단다면, 두 객체의 hashCode는 같을 수도 다를 수도 있다. 단, 다른 객체에 대해서는 다른 hashCode 값을 반환해야 해시 테이블의 성능이 좋아진다.

hashCode 재정의를 잘못했을때 가장 문제가 되는 점은 바로 아래 규약이다. `equals(Object)가 두 객체가 같다고 판단했다면, 두 객체의 hashCode도 똑같은 값을 반환해야 한다` **즉 논리적으로 같은 두 객체는 반드시 같은 hashCode를 반환해야 한다.** 

좋은 해시 함수라면 서로 다른 인스턴스에 대해서 다른 해시코드를 반환해야 한다. 이상적인 해시 함수는 주어진 서로 다른 객체에 대해서 32bit 정수 범위에 균등하게 분배해야 한다. 이상을 완벽히 구현하는것은 어렵지만 비슷하게 만들기는 어렵지 않다. 아래는 좋은 hashCode를 작성하는 간단한 요령이다.

1. int 변수 result를 선언한 후 해당 객체의 첫번째 핵심 필드로 계산한 해시코드 값을 넣는다. 여기서 핵심 필드란 equals 비교해 사용된 필드를 말한다.
2. 나머지 핵심 필드 각각에 대해서...
	3. 기본 타입필드라면, Type.hashCode()를 사용한다. Type은 기본 타입의 박싱 클래스
	4. 참조 타입필드이면, 이 클래스의 hashCode()를 사용한다. 필드 값이 null이면 상수(0)을 사용한다. 
	5. 필드가 배열이라면, 배열의 각 원소를 별도의 필드처럼 다룬다. 배열에서 사용될 핵심 원소들로 해시코드를 계산한다. 모든 원소가 핵심 원소라면 Arrays.hashCode를 사용하자. 만약 하나도 없다면 상수(0)을 넣자.
3. 2번에서 계산한 해시 코드 각각에 대해서 반복적으로 결과를 갱신한다. 옆에 식처럼 result = 31 * result + 해시코드(2번)

다른 필드로부터 계산할 수 있는 필드를 파생 필드라고 하는데 핵심 필드가 아니기 때문에 모두 무시한다. 또한 **equals 비교에 사용되지 않는 필드는 반드세 제외해야 한다.** 그렇지 않으면 논리적 동치를 만족하는 두 객체가 hashCode값이 달라지는 위험이 생길 수 있다. 

result 값을 계속해서 31을 곱하는 과정은 필드에 곱하는 순서에 따라 result값이 크게 달라지게 한다. 그 결과 클래스에 비슷한 필드가 여러 개일때 효과가 좋다. 무슨 말이냐면 만약 String의 hashCode를 곱셈 없이 구현하면 모든 아나그램(구성하는 철자는 같지만, 그 순서만 다른 문자열)의 해시 코드가 같아진다. 

값이 31인 이유는 홀수이면서 소수(prime)이기 때문인데, 만약 숫자가 짝수이고 오버플로가 발생하면 정보를 읽데 된다. 2를 곱하는 것은 시프트 연산과 같은 결과를 내기 때문이다. 소수를 곱하는 이유는 정확하지 않지만 전통적으로 그리해왔다. 결과적으로 31을 이용하면 곱셈을 시프트 연산과 뺄셈으로 대체해서 최적화할 수 있다. (31 * i 는 i << 5 - 1)과 같다.

```
@Override public int hashCode(){
	int result = Short.hashCode(shortCode);//첫번째 primitive 핵심 필드
	result = 31 * result + Object.hashCode // 두번째 참조 핵심 필드 
	result = 31 * result + Arrays.hashCode // 세번째 배열 핵심 필드 
	return result;
```

이런 식으로 만든 해시 함수 제작 요령은 최첨단은 아니지만 충분히 훌륭하다. 해시 충돌이 더욱 적은 방법을 써야한다면 Guava의 Hashing을 참고하자. 

Objects 클래스는 임의의 갯수만큼 객체를 받아 해시 코드를 계산해주는 정적 메서드인 hash를 제공한다. 이 메서드를 활용하면 앞서 구현한 코드와 비슷한 수준의 해시함수를 한줄로 작성할 수 있다. 하지만 속도는 느리다. 따라서 성능에 민감하지 않은 상황에서만 사용하자. 

클래스가 불변이고 해시 코드를 계산하는 비용이 크다면, 매번 해시코드를 호출하는 것보다 캐싱하는 방식을 고려할 수 있다.  이 객체가 해시의 키로 주로 사용될 것 같다면 인스턴스가 만들어질때 해시코드를 계산해두자. 

해시 키로 사용되지 않다면, hashCode를 불릴때 계산하는 지연 초기화 전략도 사용 가능하다. 지연 초기화를 하려면 사실 스레드 safe를 고려해야 한다. 

```
int hashCode;

@Override public int hashCode(){
	int result = hashCode;
	if(result == 0){ // Thread Safe해야 한다. 
		...
		result = 계산된 hasoCode;
		hashCode = result;
	}
	return result;
}
```

**성능을 높이기 위해 해시코드를 계산할때 필요한 핵심 필드를 생략해서는 안된다.** 속도야 빨라지겠지만, 해시 품질이 나빠져 해시 테이블의 성능을 심각하게 떨어뜨릴 수도 있다. 어떤 핵심 필드는 공통적으로 몰린 해시 코드값을 넓은 범위로 고르게 퍼트려주는 효과가 있을 수 있다. 하필 이런 핵심 필드를 생략한다면 테이블의 속도가 선형으로 느려질 것이다. 실제로 자바2 이전의 String은 문자열의 앞자리 16자리로만 해시 코드를 계산했다. URL 처럼 계층적 이름으로 대량의 문자열을 다룬다면 심각한 문제가 발생한다. 

**hashCode를 생성하는 규칙을 API 사용자에게 자세히 알려주지 말자.** 그래야 사용자가 이 값에 의지하지 않고, 추후에 계산 방식을 바꿀수도 있다. 자세한 규칙을 밝히지 않으면, 해시 기능에 결함이 발견되었을 때 더 나은 해시 방식으로 고쳐서 수정 할 수 있다. 

>핵심 정리
>equals를 재정의 할때는 hashCode도 재정의해야 한다. 그렇지 않으면 프로그램이 제대로 동작하지 않을 수 있다. 재정의한 hashCode는 Object의 API 문서에 정의된 일반 규약을 따라야 하며, 논리적 동치에 따라 다른 객체라면 해시코드가 서로 다른 값어야 좋다. 

## Item12. toString을 항상 재정의하라.

Object의 기본 toString 메서드는 우리가 만든 클래스에 적합한 문자열을 반환하는 경우는 거의 없다. 이 메서드는 단순히 `클래스_이름@16진수로_표시한_해시코드`를 반환할 뿐이다. 

toString의 일반 규약에 따라 간결하면서도 사람이 읽기 쉬운 형태의 유익한 정보를 반환해야 한다.  또한 toString 규약은 모든 하위 클래스에서 이 메서드를 재정의하라 한다. 

**equals와 hashCode만큼 중요하지 않지만, toString을 잘 구현한 클래스는 사용하기에 즐겁고, 그 클래스를 사용한 시스템은 디버깅이 쉽다.**

**실전에서 toString은 그 객체가 가진 주요 정보 모두를 반환하는게 좋다.** 하지만 객체가 거대하거나 객체의 상태를 문자열로 표현하기에 적합하지 않다면, 요약정보를 담아야 한다. 

>핵심 정리 
>모든 구체 클래스에서 Object의 toString을 재정희하자. 상위 클래스에서 이미 알맞게 정의했다면 예외다. toString을 재정의한 클래스는 사용하기 좋고 디버깅이 쉽다. toString은 객체에 대해 최대한 명확하고 유용한 정보를 읽기 좋은 형태로 반환해야 한다.

## Item13. clone 재정의는 주의해서 진행하라.

Cloneable 인터페이스의 용도는 **복제해도 되는 클래스임을 명시하는 것**이다.
하지만 의도한 목적을 제대로 이루지 못 했는데 Cloeable 인터페이스를 보면 아래와 같다. 정말 단순히 명시만 한다.
```
public interface Cloneable { } //정말 아무것도 없다 
public class Object {
	protected native Object clone() {} //protected ...
	// Object는 모든 클래스가 상속하기 한다.
}
```
실제 복제를 하는 clone메서드는 Object에 있는데 심지어 접근 제어자가 protected이다. 그래서 외부 객체에서는 다른 객체의 clone메서드를 호출할 수가 없다.

Cloneable을 구현한 클래스의 인스턴스에서 clone()을 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환한다. 반대로 Cloneable을 구현하지 않은 클래스의 인스턴스에서 clone()을 호출하면 CloneNotSupportedException을 던진다. 

이는 인터페이스를 상당히 이례적으로 사용한 예이며 따라하지 말자. 인터페이스를 구현한다는 것은 일반적으로 해당 클래스가 그 인터페이스를 구현한다는 것을 선언하는 행위다. 그런데 Cloneable은 해당 클래스가 아닌 상위 클래스(Object)에 정의된 clone()의 동작 방식을 변경한 것이다. 

실무에서는 일반적으로 Cloneable을 구현한 클래스는 clone 메서드를 public으로 제공하며, 사용자는 당연히 복제가 제대로 이뤄지리라 기대한다. 이 기대를 만족 시키려면 그 클래스와 모든 상위 클래스는 복잡하고, 강제할 수 없고, 허술하게 기술된 프로토콜을 지켜야만 하는데 그 결과로 깨지기 쉽고, 위험하고, 모순적인 매커니즘이 탄생한다. 즉 clone() 써서 생성자를 호출하지 않고도 객체를 생성할 수 있게 되는 것이다.  

clone()의 일반 규약은 허술하다. Object명세에서 가져온 설명을 보자.

'복사'의 정확한 뜻은 그 객체를 구현한 클래스에 따라 다를 수 있다. 일반적인 의도는 어떤 객체 x에 대해 아래 식은 참이다. 
```
x.clone() != x // 복사본은 원래 객체와 다르다.
x.clone().getClass() == x.getClass() 
x.clone().equals(x) 
위의 요구사항들은 반드시 만족해야 하는 것은 아니지만, 일반적으로 참이다.
```
관례상 반환된 객체와 원본 객체는 독립적이어야 한다. 이를 만족하려면 super.clone으로 얻은 객체의 필드 중 하나 이상을 반환 전에 수정해야 할 수도 있다. 

clone() 호출시 super.clone이 아닌, 생성자를 호출해 얻은 인스턴스를 반환한다고 가정해보자. 일단 반환 타입은 맞기 때문에 컴파일러는 불평하지 않을 것이다. 하지만 이 클래스의 하위 클래스에서 super.clone()을 하면 기대한것과는 다르게 생성자를 이용하여 잘못된 클래스의 객체가 만들어져, 결국 하위 클래스의 clone메서드가 제대로 동작하지 않게 된다.

> 클래스 B가 클래스 A를 상속 할때, 하위 클래스인 B의 clone은 B 타입의 객체를 반환해야 한다. 그런데 A의 clone이 자신의 생성자, 즉 new A()로 생성한 객체를 반환한다면, B의 clone()도 A 타입의 객체를 반환할 수 밖에 없다. 달리 말해, super.clone()을 연쇄적으로 호출해두면 clone이 처음 호출된 하위 클래스의 객체가 만들어 져야한다.

clone을 재정의한 클래스가 final이라면 걱정해야 할 하위 클래스가 없으니 이 관례는 무시해도 된다. 하지만 final 클래스의 clone 메서드가 super.clone을 호출하지 않는다면 Cloneable을 구현할 이유도 없다. Object의 clone 구현 동작 방식에 기댈 필요가 없기 때문이다. 

제대로 동작하는 clone 메서드를 가진 상위 클래스를 상속해 Cloneable을 구현한다고 해보자. 먼저 super.clone을 호출한다. 그렇게 얻은 객체는 원본의 완벽한 복제본일 것이다. 클래스에 정의된 모든 필드는 원본 필드와 같은 값을 갖는다. 모든 필드가 기본 타입이거나 불변 객체라면 더 손볼 것이 없다. 

그런데 쓸데 없는 복사를 지양한다는 관점에서 보면 불변 클래스는 굳이 clone 메서드를 제공하지 않는게 좋다. 

```
@Ovverride public PhoneNumber clone() 
	try {
		return (PhoneNumber) super.clone();
	} catch ( CloneNotSupportedException e) {
		throw new AssertionError(); // 일어 날 수 없다.
	}
}
```

이 메서드가 동작하게 하려면 PhoneNumber의 클래스 선언에 Cloneable을 구현하고 추가해야 한다. Object의 clone 메서드는 Object를 반환하지만 PhoneNumber의 clone 메서드는 PhoneNumber를 반환한다. 자바가 공변 반환 타이핑(covariant return typing)을 지원하니 이렇게 하는 것이 가능하고 권장하기도 한다. 달리 말해, 재정의한 메서드의 반환 타입은 상위 클래스의 메서드가 반환하는 타입의 하위 타입일 수 있다. 이 방식으로 클라이언트가 형변환을 하지 않아도 되게끔 해주자. 이를 위해서 super.clone에서 얻은 객체를 반환하기 전에 PhonbeNumber로 형변환 하였다.



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDI1ODkxNjI5LC03NjcwMzk2NTEsMTQ3Nz
g1NzIwLDg5Njk5NjUxMyw2NTE1ODg4MzcsLTkwMjI1NjUyMCw5
NzUzNDkzMCwtMTYyNzk3NTMxLDE3NDc2NDI2OTMsLTIxODY0NT
k0OSwxMTE5MDY5MjU0LDE4MzIxMTcxOTAsLTExOTYyMDMxNiwx
MjAyNjAxMzgwLDU2NzA5NzgyMywtOTAxOTczMDU2LDE2NjY0Mz
Y4ODksMTE5NTk2NDI0NywzMzAxMTkzODcsLTExNDY2MDc2MjRd
fQ==
-->