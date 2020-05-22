# Overview

데이터 베이스로부터 필요한 데이터를 가져오려면 SQL이라는 정형화된 문장을 사용해야 한다. 데이터 베이스 구조를 변경하기 위한 DDL(Data Definition Language), 테이블 레코드 조작을 위한 DML(Data Manipulation Language)가 있고 이 둘을 합쳐 SQL이라 한다.

DML에는 SELECT, INSERT, UPDATE, DELETE 쿼리로 나뉘며, 이 밖에 REPLACE, MERGE INTO 등과 같이 DBMS 벤더별로 제공되는 비표준 SQL도 있다. ANSI 표준에는 데이터를 조회하는 SELECT를 쿼리라고 하고, 그외의 INSERT, UPDATE 그리고 DELETE와 같은 SQL을 스테이먼트(Statement)라고 구분하기도 한다.

애플리케이션에서 데이터베이스와 통신할때 데이터베이스 서버로 전달되는 것은 SQL뿐이다. SQL은 어떠한(WHAT) 데이터를 요청하기 위한 언어지 어떻게(HOW) 데이터를 읽을지를 표현하는 언어는 아니므로 일반 프로그래밍 언어보다 상당히 제한적이다. 그래서 SQL을 작성하는 방법, 규칙, 내부적 처리(옵티마이저)에 대해 어느 정도 지식이 필요하다.

애플리케이션에서 코드 튜닝으로 성능을 2배 올리는 것은 굉장히 어려운 일이다. 하지만 DBMS에선 몇십배의 성능 향상은 흔한 일이다. SQL처리에서 어떻게(HOW)를 이해하고 쿼리를 작성하는 게 그만큼 중요하다는 것이다. 

이번 장에선 쿼리 패턴별로 어떻게 처리되는지를 이해하고, 많이 알려져 있지 않지만 코드수를 상당히 줄일 수 있는 유용한 쿼리 패턴도 알아보자. 

# 쿼리와 연관된 시스템 설정

SQL 작성 규칙은 대소문자 구분, 문자열 표기방법등 MySQL 서버 시스템 설정에 따라달라 진다. 시스템 설정에는 무엇이 있는지 예약어에는 어떤 것이 있고, 사용할때 주의사항도 알아보자.

## SQL 모드

MySQL 서버의 sql_mode라는 시스템 설정엔 여러 값이 동시에 설정된다. 그 중 대표적으로 SQL 작성과 결과에 영향이 있는 것들은 무엇인지 알아보자. MySQL 서버 설정 파일에서 sql_mode를 설정할때는 구분자(,)를 사용해 아래 키워드를 동시에 설정할 수 있다. 

### STRICT_ALL_TABLES

기본 MYSQL에서는 **저장하려는 값의 길이가 컬럼 길이보다 더 긴 경우라도 에러가 발생하지 않는다.** 컬럼의 길이를 초과하는 부분은 버리고 가능한 만큼만 저장한다. 물론 경고 메세지가 발생하지만, 이를 보는 사람은 별로 없을 것이다. 만약 sql_mode 시스템변수에 STRICT_ALL_TABLES를 하면 컬럼의 정해진 길이보다 큰 값을 저장할때 경고가 아닌 오류가 발생하고 쿼리 실행이 중지된다.

### STRICT_TRANS_TABLES

**컬럼에 호환되지 않는 값을 저장할때, MySQL 서버는 비슷한 값으로 최대한 바꿔서 저장하려고 한다.** 하지만 이런 부분이 사용자를 더 혼란 스럽게 하기도 한다. STRICT_TRANS_TABLES를 설정하면 맞지 않는 데이터 타입 변환이 필요할때 강제 변환을 하지 않고 에러를 발생시킨다.

### TRADITIONAL
STRICT_ALL_TABLES, STRICT_TRANS_TABLES와 비슷하지만 조금 더 엄격한 SQL 작동을 강제한다. MySQL 서버가 좀더 ANSI 표준 모드로 작동하도록 유도한다.

### ANSI_QUOTES

MySQL에서는 문자열 값(리터럴)을 표기하기 위해 홑따음표와 쌍따옴표 둘다 사용이 가능하다. 하지만 오라클과 같은 DBMS에서는 홑따옴표는 문자열 값을 표기하는데 사용하고 쌍따옴표는 컬럼 명이나 테이블 명과 같은 식별자를 구분하는 용도로만 사용한다. 

ANSI_QUOTES를 설정하면 홑따옴표만 문자열 값으로 사용할 수 있고, 쌍따옴표는 컬럼명이나 테이블 명과 같은 식별자를 표기하는데만 사용할 수 있다. 

### ONLY_FULL_GROUP_BY

MySQL의 쿼리에서는 GROUP BY 절에 포함되지 않은 컬럼이더라도 집합 함수의 사용없이 그대로 SELECT 절이나 HAVING 절에 사용할 수 있다. 이 부분도 SQL 표준이나 다른 DBMS와는 다른 동작 방식인데, 이 설정을 하면 SQL 문법에 더 엄격한 규칙을 적용하게 된다.

### PIPE_AS_CONCAT

MySQL에서는 "||"는 OR 연산자와 같은 의미로 사용된다. 이 설정을 하면 오라클과 값이 문자열 연결(CONCAT) 연산자로 사용할 수 있다.

### PAD_CHAR_TO_FULL_LENGTH








> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQwMTE4NDQ2MCwtNzI4MDk0NzY4LC0xNz
A2NDA4NTYyLDUyNTk3NDU5NywtMTE2NTAwOTQyMiwtMTc4ODU3
NzgyXX0=
-->