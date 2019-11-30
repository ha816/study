# [직렬화(Serialization)](https://www.geeksforgeeks.org/serialization-in-java/)


네트워크상에서 데이터를 어떻게 주고 무엇이 전송되는지는 다양한 방법이 있다. 사실 직렬화는 한 객체의 상태를 byte stream으로 바꾸는 매커니즘을 말한다. 역직렬화는 메모리의 byte stream에서 실제 객체를 재생성하는데 사용되는 프로세스를 말한다. 자바의 직렬화는 **JVM에서 자바 객체를 보내는 간단한 방법이다.** 

```
public class Pair implements Serializable
```
직렬화를 하려면 Pair 클래스가 Serializable 인터페이스를 구현해야 한다는 것이다. Serializable는 JVM에게 이 클래스가 직렬화 될 수 있다는 알려주는 표시자 인터페이스이다. 

주의할 점
1. If a parent class has implemented Serializable interface then child class doesn’t need to implement it but vice-versa is not true.  
2. Only non-static data members are saved via Serialization process.  
3. Static data members and transient data members are not saved via Serialization process. So, if you don’t want to save value of a non-static data member then make it transient.  
4. Constructor of object is never called when an object is deserialized.  
5. Associated objects must be implementing Serializable interface.

transient
: 데이터를  직렬화할때 하고 싶지 않은 필드가 직렬화 객체 안에 있으면 필드에 선언할 수 있다. transient가 적용된 필드는 역직렬화 할때 null을 반환한다 

SerialVersionUID
: 직렬화를 할때는 SerialVersionUID라는 각 직렬화 클래스와 상호작용하는 버전 넘버가 있다. 이것은 나중에 역직렬화를 할때 보낸 (직렬화된) 객체와 받은 (직렬화된) 객체가 부합하는지 검증하는데 사용된다.  만약 받은쪽에서 역직렬화를 할때 UID값이 다르다면, InvalidClassException을 던진다.
직렬화가 가능한 클래스는 클래스 고유의 UID를 필드에 선언이 가능한데, 반드시 정적이고 final 키워드를 붙이고 long 타입이어야 한다. 
UID를  명시하지 않아도 직렬화시 자동으로 환경에 따라서 UID를 선언한다. 그러나 모든 직렬화 가능 클래스는 UID를 명시적으로 선언하도록 하자. 왜냐하면 자동으로 UID를 계산하는 과정이 다양한 환경에 따라 매우 민감하기 때문이다. 

# XML(eXtensible Markup Language)

도메인 객체를 정의하는 데 많이 사용하는 다른 표기법은 XML이다. XML을 데이터를 구조화하는데 표현하는 언어로 대부분의 언어에서 XML을 파싱하고 기록하는 라이브러리들이 많다.

메타언어인 XSD(XML Schema Definition)은 XML문서를 작성


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTMwNzU2ODY4Ml19
-->