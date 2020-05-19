# Overview

데이터 베이스로부터 필요한 데이터를 가져오려면 SQL이라는 정형화된 문장을 사용해야 한다. 데이터 베이스 구조를 변경하기 위한 DDL(Data Definition Language), 테이블 레코드 조작을 위한 DML(Data Manipulation Language)가 있고 이 둘을 합쳐 SQL이라 한다.

DML에는 SELECT, INSERT, UPDATE, DELETE 쿼리로 나뉘며, 이 밖에 REPLACE, MERGE INTO 등과 같이 DBMS 벤더별로 제공되는 비표준 SQL도 있다. ANSI 표준에는 데이터를 조회하는 SELECT를 쿼리라고 하고, 그외의 INSERT, UPDATE 그리고 DELETE와 같은 SQL을 스테이먼트(Statement)라고 구분하기도 한다.

애플리케이션에서 데이터베이스와 통신할때 데이터베이스 서버로 전달되는 것은 SQL뿐이다. SQL은 어떠한(WHAT) 데이터를 요청하기 위한 언어지 어떻게(HOW) 데이터를 읽을지를 표현하는 언어는 아니므로 일반 프로그래밍 언어보다 상당히 제한적이다. 그래서 SQL을 작성하는 방법, 규칙, 내부적 처리(옵티마이저)에 대해 어느 정도 지식이 필요하다.




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzI1NTY0OTU1LC0xNzg4NTc3ODJdfQ==
-->