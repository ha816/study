# 원시타입

> 몇 가지 자바 원시 타입의 이름을 말하고 이 타입이  JVM에서 어떻게 처리되는지 설명하라.

원시타입(primitive type)은 Null이 될수 없다. 즉 언제나 값을 가지는 상태다. 

int, long 원시타입은 숫자 값 뒤에 L을 붙이면 long이다.  안붙이면 자연스럽게 int이다. 비슷하게 float, double도 숫자값 뒤에 D를 붙이면 double, F를 붙이면 float이다.

원시 타입의 크기

| type| 크기(bit)  |
|--|--|
|boolean | 1bit |
|char | **16(2byte)** |
|short | 16(2byte) |
|int | 32(4byte) |
|long | 64(8byte) |
|float | 32(4byte) |
|double | 64(8byte) |

char는 unsigned이므로 0 ~ 65535 까지 담을수 있다. 

> 왜 Integer.MIN_VALUES에 대응하는 양수가 없는가?
> Integer값은 $2^{32}$가지의 값을 표현할수 있다. 그리고 signed이기 때문에 절반 만큼은 음수를 위해 써야한다.  그런데 0도 양수의 표현해야하기 때문에 $2^{31}-1$만큼은 양수로 나머지는 음수로 표현한다.

# 객체 이용하기

원시 타입을 제외하면 자바 언어의 모든 변수들은 참조 타입이다. 이것들은 객체로 원시타입과 차이점은 빈객체를 의미하는 null 표현이 존재한다는 것이다.

원시 타입은 값을 메모리에 그대로 저장한다. 그에 반해 참조 값은 객체가 할당된 메모리의 주소(위치)를 저장한다. 표면적으로 둘다 비슷하게 동작하는 것으로 보이지만 실제로는 그렇지 않다. 

> final 키워드는 객체 참조에 어떤 영향을 미치는가?

객체에 선언하는 final 키워드는 원시타입에 선언하는 final 키워드와 동일하다. 즉 메모리 변수에 값이나 메모리 주소가 할당되면 그 값과 메모리 주소는 변하지 않는다. 변수에 할당된 메모리 주소는 변경하지 못하지만 객체 자체는 당연히 수정이 가능하다.

> 객체의 가시성 수정자(visibility modifier)는 어떻게 작동하는가? 

가시성 수정자는 객체의 정보은닉에 도움을 준다. 필드나 메서드, 중첩 클래스(nested class), 중첩 인터페이스(nested interface) 같은  멤버(필드, 메서드)의 접근 권한은 크게 4가지 종류가 있다. 

| 접근 권한 수정자 | 설명 |
|--|--|
|private| 선언된 멤버는 선언된 같은 클래스에서만 접근가능 |
|package-private| 선언된 멤버는 같은 패키지 안에 모든 클래스가 접근가능; 기본 접근 권한으로 알려져 있음 |
|protected| 선언된 멤버는 같은 패키지 안에 모든 클래스가 접근가능할 뿐만 아니라 다른 패키지에서 선언된 클래스를 확장하는 하위 클래스에서도 접근가능|
|public| 선언된 멤버는 어디서도 접근가능|



# 자바 배열

# String 이용하기

# 제네릭 이해하기

# 오토박싱과 언박싱

# 어노테이션

# 명명규칙 이해하기

# 예외처리 하기

# 표준 라이브러리 사용하기 

# 자바 8


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTIyOTg1MTcwMiwtMTMxODU1OTU0Niw4MT
ExOTY0MDgsLTE2NTE5NDE4MTEsLTU0ODQ1NTcwMSwtMTAzNjA1
MTg3MiwtMTUxNDczNjc0OSwtMTQ0NTc1ODA5NiwyMTM2NTQ0Mj
U5LDUyOTk3ODcwLDQwMzUyNDAwMCw4MzI4NDc2NzksMjEzNjc1
ODQwOV19
-->