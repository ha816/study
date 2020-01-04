# XML(eXtensible Markup Language)

도메인 객체를 표현하는 다른 표기법은 XML이다. XML을 데이터를 구조화하는데 표현하는 언어로 대부분의 언어에서 XML을 파싱하고 기록하는 라이브러리들이 많다.

메타언어 XSD(XML Schema Definition)는 XML문서를 이용하는 **도메인 객체를 정의하는데 사용한다.** 
즉 XSD는 객체의 정의를 말하고, 실제 객체는 XML문서로 표현한다. 특정 객체가 그 객체의 XSD 정의를 따르는지 검증도 가능하다.
자바에는 XSD 표현을 이해해서 자바 객체를 생성할 수 있는 JAXB라는 라이브러리가 있다. 


# JSON(Javascript Object Notation)

JSON은 자바스크립트에서 유래되었고 XML보다 구문이 적어 부담이 덜해 많이 사용된다.
문자열은 따옴표로 둘러싸인 텍스트고 실수와 정수는 차이가 없다. boolean 값으로 true와 false가 사용할 수 있다. 
리스트는 []대괄호로 둘러싸고 콤마로 구분된다. 객체는 {}중괄호로 둘러쌓인 키-값들의 컬렉션이며, 키들은 항상 문자열이다. 값은 null을 포함한 모든 타입을 사용할 수 있다. 

자바에서는 JSON 파싱과 처리를 위해서 Jackson이라는 라이브러리를 사용한다. 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQzNjk0Mzk0MV19
-->