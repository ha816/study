# 2장 객체의 생성과 파괴

## 생성자 대신 정적 팩토리 메서드를 고려하라.

클라이언트가 클래스의 인스턴스를 얻는 전통적 수단은 public 생성자다. 하지만 그 뿐만아니라 클래스는 생성자와 별도로 **정적 팩터리 메서드(static factory method)**를 제공할 수 있다. 

예를 들어 boolean의 박싱 클래스인 Boolean 클래스를 보자. 
```
public static Boolean valueOf(boolean b) {
	return b ? Boolean.true : Boolean.false;
}
```
>주의!!
>여기서 이야기하는 정적 팩터리 메서드는 디자인 패턴에서의 팩터리 메서드(Factory Method)와 다르다. 디자인 패턴 중에서 이와 일치하는 패턴은 없다. 

### 정적 팩터리 메서드의 장점과 단점
먼저 장점 다섯 가지를 알아보자

#### 1. 이름을 가질 수 있다.  
생성자에 넘기는 매개변수와 생성자 자체만으로는 반활된 객체의 특성을 제대로 설명하지 못한다.





From, With,
Cleaner;
try with resource

equals, 재정의시는 hashCode

@override를 반드시 써라
오버로드로 햇갈리면 컴파일 에러가 뜬다. 

1.  builder 패턴 

서비스 로직을 catch에서 처리하지마라 




<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5Mzg4MDE3OTksMTU1MDUxMzI5NiwtMT
EzNzcxOTAxNV19
-->