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
> Integer값은 $2^{32}$가지의 값을 표현가능하다. 가능하다. 그렇게 때문에 signed이기 때문에 


# 객체 이용하기

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
eyJoaXN0b3J5IjpbMTk3OTQ2MDc3NCw1Mjk5Nzg3MCw0MDM1Mj
QwMDAsODMyODQ3Njc5LDIxMzY3NTg0MDldfQ==
-->