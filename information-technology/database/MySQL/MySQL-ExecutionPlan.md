# Overview

DBMS에서는 요청이 들어온 쿼리를 항상 같은 방법으로 똑같은 결과를 내는 것이 아니다. 아주 많은 방법이 있지만 그중에서 어떤 방법이 최적이고 최소의 비용으로 결과를 낼지 결정해야 한다. 이를 위해 DBMS는 테이블의 데이터가 어떤 분포로 저장되어 있는지 통계 정보를 참조하고, 그러한 기본 데이터를 비교해 최적의 실행 계획을 수립한다. 이 작업을 DBMS에서는 옵티마이저가 담당한다. 

MySQL에서는 EXPLAIN 명령을 통해 실행 계획을 확인이 가능하다. 이 장에서는 실행 계획에 표시되는 내용이 무엇인지, MySQL 서버가 내부적으로 어떤 작업을 하는지 알아보겠다. 

## 쿼리 실행 절차

* SQL 파싱
	* 요청이 들어온 쿼리 문장을 잘깨 쪼개서 MySQL서버가 이해할 수준으로 분리한다.
* 최적화 및 실행 계획 수립
	* SQL의 파싱 정보를 확인하며, 어떤 테이블부터 읽고 어떤 인덱스를 이용해 테이블을 읽을지 결정한다.
* 실행 계획
	* 결정된 읽기 순서나 선택된 인덱스를 이용해 스토리지 엔진으로 부터 데이터를 가져온다.

첫번째 단계는 SQL 파싱으로 MySQL 서버의 SQL 파서라는 모듈로 처리한다. 이 과정에서 SQL 문장에 문법적 오류가 있다면 걸러진다. 또한 이 단계에서 SQL 파스 트리가 만들어진다. MySQL 서버는 엄밀히 말해서 SQL 문장 그 자체가 아니라 SQL 파스트리를 이용해 쿼리를 실행한다. 

두번째 단계는 SQL 파스 트리를 참조하고 많은 일을 처리한다. 대표적으로 불필요한 조건의 제거 및 복잡한 연산의 단순화, 여러 테이블의 조인이 있는 경우 **어떤 순서로 테이블을 읽을지 결정**, 각 테이블에 사용된 조건과 인덱스 통계정보로 **사용할 인덱스 결정**, 가져온 레코드를 임시 테이블에 넣고 다시 한번 가공해야하는지 결정

세 번째 단계는 수립된 실행 계획대로 스토리지 엔진에 레코드를 읽어오도록 요청하고 , MySQL 엔진에서는 스토리지 엔진으로 부터 받은 레코드를 조인하거나 정렬하는 작업을 한다. 

첫 번재와 두 번재 단계를 거의 MySQL 엔진에서 처리하며, 세번째 단계는 MySQL 엔진과 스토리지 엔진이 동시에 참여해서 처리한다. 

## Optimizer

옵티마지어의 종류에는 크게 두 종류가 있다. 현재 대부분의 DBMS가 선택하고 있는 옵티마이저는 비용 기반(Cost-based optimizer, CBO) 옵티마이저 또는 규칙 기반 최적화 방법(Rule-based optimizer, RBO)로 나누어진다.

* 규칙 기반 최적화는 기본적으로 테이블 레코드 건수나 선택도 등을 전혀 고려하지 않고, 옵티마이저에 내장된 우선순위에 따라 실행계획을 수립한다.  따라서 같은 쿼리에 대해서는 거의 항상 같은 실행방법을 만들어 낸다. 과거에 각 테이블이나 인덱스의 통계정보가 거의 없고, 상대적으로 느린  CPU 연산탓에 비용 계산이 부당스러웠기 때문에 사용되었었다. 현재는 대부분 비용기반 옵티마이저를 채택하고 있다. 
* 비용 기반 최적화는 쿼리를 처리하기 위해 여러 가능한 방법을 만들고, 각 단위 작업의 비용(부하) 정보와 대상 테이블의 예측된 통계를 이용해서 각 실행 계획별 비용을 산출한다. 이렇게 산출된 각 실행 방법별로 최소 비용이 소요되는 처리 방식을 선택해 최종 쿼리를 실행한다. 

## Statistical Information

비용기반 최적화 기법에서 가장 중요한 것은 통계정보다. 통계정보가 정확하지 않다면 전혀 엉뚱한 방향으로 쿼리를 실행할 수 도 있다. 예를 들어 1억건의 레코드가 저장된 테이블에서 통계정보가 제대로 갱신되지 않아 10건의 레코드만 있는 것으로 되어 있다면, 실제 쿼리 수행시 인덱스 레인지 스캔이 아니라 풀 테이블 스캔을 실행해 버릴 수도 있다. 

기본적으로 MySQL에서 관리되는 통계 정보는 대략의 레코드 건수와 인덱스의 유니크한 값의 개수 정도가 전부다. 그에 반해 오라클과 같은 DBMS에는 통계정보가 상당히 정적이고 수집에 많은 시간이 소요되기 때문에 통계정보만 따로 백업하기도 한다. 

MySQL에서 통계 정보는 사용자가 알아채지 못하는 순간순간 자동으로 변하기 때문에 상당히 동적이다. 하지만 레코드 건수가 많지 않으면 통계 정보가 상당히 부정확한 경우가 많다. 이때 ANALYZE명령으로 강제적으로 통계정보를 갱신해야 할때도 있다. 

```
SHOW TABLE STATUS LIKE 'table'
SHOW INDEX FROM table
```
통계 정보를 갱신하려면 ANALYZE를 실행하면 된다. 
```
-- // 파티션을 사용하지 않는 일반 테이블의 통계 정보 수집 
ANALZE TABLE table

-- // 파티션을 사용하는 테이블에서 특정 파티션의 통계 정보 수집
ALTER TABLE table ANYALZE PARTITION p3;
```
ANALYZE를 실행하는 동안 InnoDB 테이블은 읽기와 쓰기 모두 불가능하다. 따라서 서비스 도중에는 ANALYZE를 실행하지 않는 것이 좋다. InnoDB 테이블은 인덱스 페이지 중에서 8개 정도만 랜덤하게 선택해서 본석하고 그 결과를 인덱스의 통계정보로 갱신한다. 

# Execution Plan

MySQL에서 쿼리 실행 계획을 활용하려면 EXPLAIN 키워드를 붙이면 된다. 추가적으로 **EXPLAIN EXTENDED나 EXPLAIN PARTITIONS** 명령을 이용하면 더 상세한 실행 계획을 확인할 수도 있다. 추가 옵션을 사용하는 경우는 기본적인 실행 계획에 추가로 1개의 정보가 더 표시된다.

EXPLAIN을 실행하면 쿼리 문장의 특성에 따라 표 형태로 된 1줄 이상의 결과가 표시된다. **표의 각 라인(레코드)은 쿼리 문장에서 사용된 테이블의 개수만큼 나온다.** 서브 쿼리로 임시 테이블을 생성한 경우 그 임시 테이블도 포함된다. 

실행 순서는 위에서 아래로 순서대로 표시된다. UNION이나 상관 서브 쿼리와 같은경우 순서대로 나오지 않는 경우도 있다. 

실행 계획에서 위쪽에 출력된 레코드(id 컬럼의 값이 작을 수록) 쿼리의 바깥(Outer) 부분이거나 먼저 접근한 테이블이고, 아래쪽에 출력된 레코드(id 컬럼의 값이 클수록) 쿼리의 안쪽(Inner) 부분 또는 나중에 접근한 테이블이다. 


![enter image description here](https://www.mysql.com/common/images/enterprise/query_analyzer_explain.png)

## id

실행 계획에서 가장 왼쪽에 표시되는 id 컬럼은 단위 SELECT 쿼리 별로 부여되는 식별자 값이다. 

사실 하나의 SELECT 문장은 다시 1개 이상의 하위(SUB SELECT) 문장을 포함할 수 있다. 단위 SELECT 쿼리는 하나의 SELECT 문장을 분리해낸 SELECT 문장을 말한다. 

만약 하나의 SELECT 문장 안에서 여러 테이블을 조인하면 조인되는 태이블의 개수만큼 실행 계획 레코드가 출력되지만 같은 id가 부여된다. 

```
EXPLAIN
SELECT * FROM employees e, salaries s
WHERE e.emp_no = s.emp_no
```

반대로 한 쿼리 문장이 다수의 단위 SELECT 쿼리로 구성되어 있으면 각 레코드가 다른 id를 지닌다.
```
EXPLAIN
SELECT ( 
SELECT COUNT(*) FROM employess + SELECT COUNT(*) FROM departments )
```

## select_type

각 단위 SELECT 쿼리가 어떤 타입인지 알려준다. 

### SIMPLE

UNION이나 서브 쿼리를 사용하지 않는 단순한 SELECT 쿼리인 경우, select_type에 SIMPLE로 표시된다. 쿼리 문장이 아무리 복잡하더라도 실행계획에서 SIMPLE인 단위 쿼리는 반드시 하나만 존재한다. 

### PRIMARY

UNION이나 서브 쿼리가 포함된 SELECT 쿼리의 실행계획에서 가장 바깥족(Outer)에 있는 단위 쿼리는 PRIMARY로 표시된다. SIMPLE과 마찬가지로 PRIMARY는 SELECT 쿼리에서 하나만 존재하며, 쿼리의 바깥쪽에 있는 단위 쿼리가 PRIMARY로 표시된다.

### UNION

UNION으로 결합하는 단위 SELECT 쿼리 가운데 첫 번째를 제외한 두 번째 이후 단위 SELECT 쿼리의 select_type은 UNION으로 표시된다. UNION의 첫 번재 단위 SELECT는 UNION이 아닌 UNION 쿼리로 결합된 전체 집합의 select_type이 표시된다. 

```
EXPLAIN
SELECT * FROM (
	(SELECT emp_no FROM employees e1)
	UNION ALL
	(SELECT emp_no FROM employees e2)
	UNION ALL
	(SELECT emp_no FROM employees e3)
) tb;
```

위 쿼리 실행 계획은 아래 와같다. UNION이 되는 단위 SELECT 쿼리 3개 중에서 첫번째는 UNION이 아닌 DERIVED 이고, 나머지는 UNION이다. UNION으로 묶인 세 개의 서브 쿼리는 임시 테이블을 만들어서 사용하므로 DERIVED라는 타입을 갖는 것이다.

|id| select_type|
|--|--|
|1 | PRIMARY |
| 2 | DERIVED|
|  3| UNION|
|  4| UNION|

### DEPENDENT UNION

DEPENDENT UNION은 UNION이나 UNION ALL로 집합을 결합하는 쿼리에 표시된다. 추가적으로 **DEPENDENT는 UNION 이나 UNION ALL로 결합된 단위 쿼리가 외부의 영향을 받는 것을 의미한다.** 

>UNION VS UNION ALL
>결과 레코드의 중복을 제거 여부
UNION은 모든 컬럼이 같은 결과에 대해서는 중복 결과를 제거하지만 UNION ALL은 제거하지 않는다. UNION을 쓰면 중복을 제거하기 위해 추가적인 작업을 반드시 해야하기 때문에 성능 이슈가 있을 수 있다. 하지만 보통 우리는 중복값을 필요로 하는 경우가 없다. 

아래 쿼리의 서브 쿼리 두개가 UNION으로 결합되어 있다. 이 서브 쿼리를 보면 외부(Outer)에서 정의된 employees 테이블의 emp_no 컬럼을 사용하고 있다. 즉 내부 쿼리가 외부의 값을 참조해서 처리 될때 DEPENDENT 키워드가 표시된다.

```
EXPLAIN
SELECT e.first_name,
	( 
	SELECT CONCAT('Salary', COUNT(*)) AS message
	FROM salaries s WHERE s.emp_no = e.emp_no
	UNION
	SELECT CONCAT('Department', COUNT(*)) AS message
	FROM dept_emp de WHERE de.emp_no = e.emp_no
	) AS message
FROM employees e
WHERE e.emp_no = 10001;
```

|id| select_type|
|--|--|
|1 | PRIMARY |
| 2 | DEPENDENT SUBQUERY|
|  3| DEPENDENT UNION|

주의 
> 하나의 단위 SELECT 쿼리가 다른 단위 SELECT를 포함하고 있으면 포함된 쿼리를 서브 쿼리라고 한다. 이처럼 서브 쿼리가 사용된 경우에는 외부(Outer) 쿼리보다 서브 쿼리가 먼저 실행되는것이 일반적이고 이 방식이 반대의 경우보다 보통 빠르다. 하지만 DEPENDENT 키워드를 포함하는 서브 쿼리는 외부 쿼리에 의존적이므로 절대 외부 쿼리보다 먼저 실행할 수없다. 그래서 DEPENDENT 키워드가 포함된 쿼리는 비효율적인 경우가 많다. 

### UNION RESULT

UNION 결과를 담아두는 테이블을 의미한다. MySQL에서 UNION ALL 이나 UNION 쿼리는 모두 UNION 결과를 임시 테이블에 생성한다. 실행 계획 상에서 이 임시 테이블을 말하는 라인이 UNION RESULT이다.  UNION RESULT는 실제 쿼리에서 단위 쿼리가 아니기 때문에 별도로 id값은 부여되지 않는다. 

```
EXPLAIN
SELECT emp_no FROM salaries WHERE salary > 100000
UNION ALL
SELECT emp_no FROM dept_emp WHERE from_date > '2001-01-01'
```

|id| select_type|table|
|--|--|--|
|1 | PRIMARY |salaries|
| 2 | UNION| dept_emp|
||UNION RESULT|<union1,2>|

<union1,2> 에서 1,2는 SELECT 쿼리의 id를 말한다.

### SUBQUERY

일반적으로 서브 쿼리라고 하면 여러 쿼리를 통틀어 이야기한다. 여기서 SUBQUERY라는 것은 FROM절을 제외하고 사용되는 서브 쿼리만을 의미한다.

```
EXPLAIN
SELECT 
	e.first_name,
	(SELECT COUNT(*) FROM dept_emp de, dept_manager dm
	WHERE dm.dept_no = de.dept_no) AS cnt
FROM employees e
WHERE e.emp_no = 10001;
```

|id| select_type|table|
|--|--|--|
|1 | PRIMARY |e|
| 2 | SUBQUERY| dm|
|2|SUBQUERY|de|

MySQL의 실행 계획에서 **FROM 절에 사용된 서브 쿼리는 DERIVED**라고 표시되고, 그 밖의 위치에서 사용된 서브 쿼리는 전부 SUBQUERY라고 표시된다. 파생 테이블이라는 단어는 DERIVED와 같은 의미로 이해하면 된다.

서브 쿼리는 사용되는 위치에 따라 각각 다른 이름을 지닌다.

* 중첩된 쿼리(Nested Query)
	* SELECT 되는 컬럼에서 사용된 서브 쿼리를 네스티드 쿼리라고 한다.
* 파생 테이블(Derived Query)
	* FROM 절에 사용된 서브 쿼리를 MySQL에서는 파생 테이블이라 부른다.
* 서브 쿼리(Sub Query)
	* WHERE 절에 사용된 경우에는 일반적으로 그냥 서브 쿼리라고 한다.

### DEPENDENT SUBQUERY

서브 쿼리가 바깥쪽(Outer) SELECT 쿼리에서 정의된 컬럼을 사용하는 경우를 DEPENDENT SUBQUERY라 한다. 

```
EXPLAIN
SELECT e.first_name,
	( SELECT COUNT(*)
	FROM dept_emp de, dept_manager dm
	WHERE dm.dept_no = de.dept_no AND de.emp_no = e.emp_no) AS cnt
FROM employees e
WHERE e.emp_no = 10001;
```

이럴때는 안쪽 서브 쿼리가 바깥쪽 SELECT 쿼리에 컬럼에 의존하기 때문에 DEPENDENT 키워드가 붙고 DEPENDENT UNION처럼 외부 쿼리가 먼저 수행된 후에 내부 쿼리가 실행되어야 하므로 DEPENDENT 키워드가 붙은 쿼리는 없는 것 보다 처리 속도가 느릴때가 많다.

|id| select_type|table|
|--|--|--|
|1 | PRIMARY |e|
| 2 | DEPENDENT SUBQUERY| de|
|2|DEPENDENT SUBQUERY|dm|

> Written with [StackEdit](https://stackedit.io/).

### DERIVED

서브 쿼리가 **FROM 절에 사용될 경우 MySQL 항상 DERIVED인 실행 계획을 만든다.** DERIVED는 단위 SELECT 쿼리의 실행 결과를 메모리나 디스크에 임시 테이블을 생성하는 것을 의미한다. 이 임시 테이블을 파생 테이블이라고도 한다. 

안타깝게도 **FROM 절에 사용된 서브 쿼리는 제대로 성능 최적화를 못할때가 많다. 파생 테이블에는 인덱스가 전혀 없으므로 다른 테이블과 조인할때 성능상 불리할때가 많다.** 

```
EXPLAIN
SELECT *
FROM ( SELECT de.emp_no FROM dept_emp de) tb,
	employees e
WHERE e.emp_no=tb.emp_no;
```

|id| select_type|table|
|--|--|--|
|1 | PRIMARY |<derived2>|
| 1| PRIMARY| e|
|2|DERIVED |de|

> 쿼리 튜닝을 하기 위해 실행 계획을 확인할때는 select_type에 DERIVED가 있는지 확인해야 한다. 다른 방법이 없어서 서브 쿼리를 사용하는 것은 피할 수 없지만 조인으로 해결할 수 있다면 서브 쿼리보다 조인을 활용할것은 권장한다. 

### UNCACHEABLE SUBQUERY, UNION

하나의 쿼리 문장에서 서브 쿼리가 하나만 있더라도 실제 그 서브 쿼리가 한번만 실행되는 것은 아니다. 조건이 똑같은 서브 쿼리가 실행될때는 다시 실행하지 않고 이전 실행 결과를 그대로 사용할 수 있게 쿼리의 결과를 캐시 공간에 담아둔다.  이 서브 쿼리 캐시는 쿼리 캐시나 파생 테이블과는 전혀 무관한 기능이므로 혼동하지 말자.

* SUBQUERY는 바깥쪽(Outer)의 영향을 받지 않으므로 처음 한번 실행한 후 그 결과를 캐시해두고 필요할때 재사용한다. 
* DEPENDENT SUBQUERY는 의존하는 바깥쪽(OUTER) 쿼리의 칼럼의 값 단위로 개시해두고 사용한다.

DEPENDENT SUBQUERY는 외부 쿼리의 값을 단위로 캐시가 만들어진다. 
종종 서브 쿼리에 포함된 요소에 의해 캐시 자체가 불가능할수 있다. 대표적으로는 아래와 같다. 

* 사용자 변수가 서브 쿼리아 사용된 경우
* NOT-DETERMINISTIC 속성의 스토어드 루틴이 서브 쿼리에 있는 경우
* UUID()나 RAND()함수처럼 호출할때 마다 함수가 달라지는 경우

## table 컬럼

MySQL 실행 계획은 단위 SELECT 기준이 아니라 테이블 기준으로 표시된다. 만약 테이블의 이름에 별칭이 부여된 경우에는 별칭이 표시된다. 별도로 테이블을 사용하지 않은 경우 table에는 NULL이 표시된다.

Table 칼럼에 <derived> 또는 <union>과 같이 <> 둘러싸인 이름이 명시되는 경우는, 임시테이블을 의미한다. 그리고 <>안에 표시되는 숫자는 단위 SELECT 쿼리의 id를 지칭한다. 

<derived 2>라는 것은 2번 단위 SELECT 쿼리의 실행 계획으로 만들어진 파생 테이블을 말한다.

|id| select_type|table|
|--|--|--|
|1| PRIMARY |derived2|
|1| PRIMARY| e|
|2|DERIVED |dept_emp|

위 첫번째 라인의 table 컬럼이 derived2인데, 이것은 단위 SELECT 쿼리의 아이디가 2번 실행 계획으로부터 만들어진 파생 테이블을 말한다. 이 파생 테이블은 dept_emp 테이블로 부터 SELECT된 결과가 저장된 파생 테이블이라는 점을 알 수 있다. 

1. 첫번째 라인이 <derived2>인것을 보아 이 라인 보다 id가 2번인 라인을 먼저 실행하고 그 결과가 파생 테이블로 준비되야 한다는 것을 알 수 있다. 
2. 세번째 라인의 DERIVED를 보면, dept_emp 테이블을 읽어 파생 테이블을 생성하는 것을 알 수 있다. 
3. 첫번째 라인과 두번째 라인은 같은 id를 가지는 것으로 보아 2개 테이블(derived2, e)이 조인되는 쿼리라는 것을 알 수 있다. 그런데 derived2 테이블이 e 테이블 보다 먼저 왔기 때문에 derived2가 드라이빙 테이블이 되고, e는 드리븐 테이블이 된다. 즉 derived2 테이블을 먼저 읽어 e 테이블을 조인 했다는 것이다. 

> **MySQL은 다른 DBMS와 달리 FROM 절에 사용된 서브 쿼리(Derived, 파생 테이블)은 반드시 별칭을 가져야 한다.** 그렇지 않으면 별칭이 부여되지 않았다는 에러 메세지가 출력되고 쿼리는 실행되지 않을 것이다. 쿼리를 작성하거나 실행 계획을 확인할때는 임시 테이블의 별칭을 잊지 말고 명시해야 한다. 

## type

쿼리 실행 계획에서 type 이후 컬럼은 MySQL 서버가 테이블 레코드를 어떤 방식으로 읽었는지를 의미한다. 방식이란 인덱스를 사용해 레코드를 읽었는지 아니면 테이블을 처음부터 끝까지 읽는 풀 테이블 스캔으로 읽었는지 등을 의미한다. **type 컬럼은 인덱스를 효율적으로 사용하는지를 알려주기 때문에 반드시 체크해야할 중요한 정보다.**

MySQL 메뉴얼에서는 type 컬럼을 조인(Join) 타입으로 소개한다. MySQL에서는 하나의 테이블로 부터 레코드를 읽는 것도 조인처럼 처리한다. 그래서 태이블 갯수에 관계없이 모두 조인 타입으로 명시하고 있다. 하지만 조인 타입으로 생각하지 말고 각 테이블의 접근 방식(Access type)으로 이해하면 좋다. 

* system
* const
* eq_ref
* ref
* fulltext
* ref_or_null
* unique_subquery
* index_subquery
* range
* index_merge
* index
* ALL

총 12가지 접근 방법 중에서 ALL을 제외한 나머지는 모두 인덱스를 사용한 접근 방식이다. 하나의 단위 SELECT 쿼리는 위의 접근 방법 중에서 단 하나만 사용할 수 있다. 또한 index_merge를 제외한 나머지 접근 방법은 반드시 하나의 인덱스만 사용한다.   따라서 실행 계획에서 각 라인에 접근 방법이 2개 이상 표시 되지 않으며, index_merge 이외에 type 에서는 인덱스 항목에도 단 하나의 인덱스 이름만 표시된다. 

참고로 위의 나열된 접근 방식은 빠른 순서대로 나열된 것이다. MySQL 옵티마이저는 이런 접근 방식과 비용을 함께 계산하여 최소의 비용이 필요한 접근방식을 선택한다.

### system

레코드가 1건만 존재하는 테이블 또는 한건도 존재하지 않는 테이블을 참조하는 접근 방법이다. MyISAM이나 MEMORY 테이블에서만 사용되는 접근 방법이다. InnoDB는 나타나지 않는다. InnoDB에서는 ALL 또는 index로 나타날 가능성이 크다. 사실 레코드가 1건 이하인 경우에만 볼 수 있으므로 거의 볼수 없다.

### const

**프라이머리 키나 유니크 키 컬럼을 이용하는 WHERE조건 절을 가지고 있고, 반드시 1건을 반환하는 방식이다.** 다른 DBMS에서는 유니크 인덱스 스캔(UNIQUE INDEX SCAN)이라고도 한다.
```
EXPLAIN  SELECT * FROM employyess WHERE emp_no = 10001; 
-- UNIQUE KEY WHERE PHRASE
```
const는 쿼리를 최적화하는 단계에서 모두 상수화를 한다, 그래서 const이다. 
```
EXPLAIN
SELECT COUNT(*) 
FROM employees e1
WHERE first_name = (
SELECT first_name FROM employees e2 WHERE emp_no = 100001
) -- it's const
```
다중 컬럼으로 구성된 프라이머리키나 유니크 키 중에서 인덱스의 일부 컬럼만 조건으로 사용할 때는 const 타입의 접근법을 사용할 수 없다. 왜냐하면 실제 레코드가 1건만 있다 하더라도 MySQL 엔진인 데이터를 읽어보기 전에는 레코드가 1건이라고 확신할 수 없기 때문이다. 이렇게 일부만 조건으로 사용하면 const가 아닌 ref가 표시된다.

당연히 다중 컬럼으로 구성된 프라이머리키나 유니크 키 모든 컬럼을 동등 조건으로 WHERE 절에 사용하면 const가 표시된다.

### eq_ref

eq_ref(동등 참조)는 여러 테이블이 조인되는 실행 계획에서만 표시된다. 처음 읽은 테이블의 컬럼 값을 그 다음 조인할 테이블의 프라이머리키나 유니크 키 컬럼의 검색 조건에 사용할 때 eq_ref가 나타난다. 즉 조인되는 테이블에서 반드시 1건의 레코드만 존재한다. 

조인되는 테이블의 유니크 키로 검색할 때는 인덱스가 NOT NULL 이어야 하고, 다중 컬럼이라면 모든 컬럼이 비교 조건에 사용되어야 eq_ref가 가능하다.

```
EXPLAIN
SELECT * FROM dept_emp de, employees e -- de JOIN e
WHERE e.emp_no = de.emp_no AND de.dept_no = 'd005' 
-- e.emp_no is Primary key. Exist one record.
```

|id| select_type|table| type|
|--|--|--|--|
|1| SIMPLE |de|ref|
|1| SIMPLE| e| eq_ref|

### ref

eq_ref와는 달리 조인의 순서와 관계 없으며, 또한 프라이머리나 유니크 키등의 제약 조건도 없다. 인덱스 종류와 관계없이 동등조건으로 검색할때는 ref 접근 방법이 된다. 

eq_ref에서 존재하던 레코드가 1건이라는 보장이 없지만 동등한 조건으로만 비교되므로 매우 빠르다, 

```
EXPLAIN
SELECT * FROM dept_emp WHERE dept_no = 'd005'
```

|id| select_type|table| type|
|--|--|--|--|
|1| SIMPLE |dept_emp|ref|

위의 예제에서는 dept_emp 테이블의 프라이머리 키를 구성하는 컬럼(dept_no + emp_no) 중에서 일부(dept_no)만 사용됬기 때문에 결과 레코드가 1건이라는 보장이 없다. 그래서 const가 아닌 ref 접근 방법이 사용되었다. 

### const, eq_ref, ref

* const
	* 조인 순서와 관계없이 프라이머키리나 유니크 키의 모든 컬럼에 대해 동등 조건으로 반드시 1건의 레코드를 반환
* eq_ref
	* 조인에서 첫번째 읽은 테이블의 컬럼값이 두번째 테이블의 프라이머리나 유니크 키로 동등조건 검색 된 경우
* ref
	* 조인 순서와 인덱스 종류
* 


## possible_keys

## key

## key_len

## ref





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc1OTUwMTA3OSw3NzYyNTg4NTYsLTE4Mj
A5NDUzNzQsNzgzOTQzNjYzLC0xMjYwOTE3MjgxLC00NjA3NjMy
NTEsNzgzNzcwOTgyLC0xMzQyNjgwNjcwLC0xNjYzNzE0NzY3LC
0yMTE4MDMxNjUyLDUzNTgzMTU0NiwxMzM1OTc1Njg5LDI4ODc1
OTIwLC03MDM0NjM2OTcsLTE2MjgxNTE5MzYsNjQzMzE2Nzc1LD
k1NjY2NjIwLDM2MDU0MzQ2MSwtMzM5MDY0NDA3LC03NDQ3NDI4
NTBdfQ==
-->