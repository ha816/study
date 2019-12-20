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

 equals 메서드를 재정의할때는 반드시 일반 규약을 따라야한다. 다음은 Object명세에 적힌 규약이다. equals 메서드는 동치관계(equivalence relation)을 구현하며 아래 조건을 만족해야 한다. 

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
일관성은 두 객체가 같은 상황이라면 ( 어느 한 객체 또는 두 객체 모두가 수정되지 않는한) 앞으로도 영원히 같아야 한다는 점이다. 가변 객체는 비교 시점에 따라 달라질수도 있지만, 불변 객체는 한번 다르면 영원히 달라야 한다. 클래스를 작성할때 불변 클






> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDgzNjIxNiwxNzY1NDQ4OTYyLC0xOTEyMj
c0MTUyLC00ODI4MTM0NSwtMzY1Mjk0NjMwLC0xMDI4MTM4OTQ4
LDEzODQwOTcyMDMsMTY0Mjc4MjI5NSwtMTI5MjMwOTMyMywxNz
I1NjczMDUsOTE3MDU5MDQ1LDM2NjE0NTk3NiwxMjQ5ODI1NzEz
LC02MTcxMjA4NiwxMjgwMTgwMDQ2LDE1OTc1NDQ5NCwtNzE2NT
g2MDc3LC0yMDczMDc3Njk4LC05MjgzNTE5MDUsLTY4MTExMTM3
MV19
-->