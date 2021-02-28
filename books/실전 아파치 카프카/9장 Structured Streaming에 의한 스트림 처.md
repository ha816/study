# Overview 

이 장에서는 외부 스트림 처리의 예로 아파치 스파크의 검포넌트 중 하나인 Structured Streaming을 사용해 카프카와 조합한 스트림 처리 애플리케이션을 조립하는 예를 알아보자. 

간단히 Structured Streaming의 개요에 대해 설명하고, 작성할 예제 애플리케이션의 동작 환경에 대해 설명한다. 

스파크와 Strucutred Streaming은 스칼라, 파이썬 자바 등으로 애플리케이션 개발이 가능한데, 애플리케이션 개발에서는 가장 많이 사용하는 언어중 하나인 스칼라를 사용한다. 

# 아파치 스파크

아파치 스파크는 OSS(오픈소스 소프트웨어)의 병렬 분산 처리 프레임워크다. 하둡의 맵리듀스 프레임워크와 마찬가지로 여려 범용 서버로 구성된 클러스터를 사용해 대규머 데이터의 배치 처리를 병렬로 실행할 수 있다. 

스파크는 맵리듀스 프레임 워크에 비해 더 효율적으로 데이터를 처리할 수 있도록 설계되어 있다. 이외에도 다양한 프로그래밍 언어로 애플리케이션 개발이 가능한 점이나, 머신러닝, 쿼리 처리, 그리고 이 장의 주제인 스트림 처리 등 특정 용도에 있어서 병렬 분산 처리를 쉽게 활용할 수 있는 컴포넌트가 포함되어 있다는 점도 장점이다. 

스파크로 개발한 애플리케이션은 클러스터 관리자에 의해 계산 리소스가 관리되는 클러스터에서 동작한다. 클러스터 관리자에는 하둡의 YARN과 아파치 Mesos, 스파크에 포함된 Standalone이 있으며, 쿠버네티스도 이를 지원하고 있다. 

## 스파크의 데이터 처리 모델

스파크는 처리 대상 데이터를 RDD(Resilient Distributed Dateset; 탄력이 있게 분산된 데이터 셋)라 불리는 내장애성을 지닌 분산 컬렉션으로 추상화 한다. 즉 처리 대상의 레코드 하나하나를 RDD로 취급한다. 

```
// 아래 각 레코드는 모든 하나의 추상화된 컬렉션 구조로 취급된다.
고양이, 개, 여우, 너구리
// 각 레코드는 RDD의 한 요소로 추상화된다.
```

RDD는 데이터 처리을 위한 추상화된 인터페이스로 이해할 수 있다. 레코드를 RDD로 추상화하여 개발자는 분산 처리를 의식하지 않고 컬렉션 처리 코드를 작성하여 애플리케이션 개발이 가능하다. 스파크는 처리 대상의 파일이나 RDBMS의 데이블 등 처리 대상의 데이터에 대한 RDD 인터페이스를 부여하는 수단과 기능을 제공한다. 

```
val rdd = sc.textFile("data.txt");
val wordCountRDD = rdd.flatMap(_.split("" ).map(word => (word, 1))).reducesByKey(_+_)

wordCountRdd.savaAsText("result.txt");
```

개별 RDD요소에 정의한 map과 부여한 조건에 일치하는 요소를 거르는 filter, 특정 키별로 요소를 그룹화해서 보여주는 reduceByKey 등 미리 정의된 함수를 적용하여 데이터 처리 로직을 작성 가능하다. 

이처럼 RDD에 대한 여러 처리를 위한 함수를 변환이라고 한다. 변환 하나하나는 단순한 데이터를 처리하는 경우가 많지만, 여러 변환을 체인으로 연결하여 복잡한 데이터 처리 로직을 작성할 수 있다. 

데이터 처리 결과는 액션이라는 종류의 함수를 사용하여 그 결과를 취급한다. 액션 함수 중 하나로 saveAsText는 처리 결과를 파일로 저장할 수 있다.

개발자가 작성한 데이터 처리 로직은 스케쥴러등과 함께 Driver Program에 모이게 된다. Driver Program은 Driver라고 불리는 프로세스에 실행된다. 스파크 애플리케이션의 경우 클러스터 내의 단일 슬레이브 서버에서 Driver를 실행하는 경우와 스파크 애플리케이션을 실행하는 클라이언트가 Driver를 겸업하는 경우가 있다. 

스케줄러는 개발자가 작성한 처리 로직을 클러스터 상에서 처리하기 위한 분산 처리를 제어한다. 스케줄러는 작성된 처리 로직을 병렬 처리 가능한 단위인 Task라는 단위로 쪼개고 실행 순서를 조절한다. 

태스크를 실행하는 슬레이브 서버에서 동작하는 Executor라 불리는 프로세스다. 스파크에서는 클러스터 내부의 여러 Executor가 병렬로 태스크를 실행함으로써 전체 클러스터에서 병렬 분산 처리를 하고 있다. 


## DateFrame / Dataset

RDD는 스파크를 이용하는데 가장 기본적인 데이터 구조이자 인터페이스다. 그러나 복잡한 처리에서는 최적화가 어렵고 처리 내용을 파악하기 어려워 개발 언어에 따라서 성능의 차이가 생길 수 있다. 

클러스터 안에서 병렬 처리시 Executors는 JVM에서 동작하지만, 데이터 처리 로직을 작성한 프로그래밍 언어에 따라서는 처리 부분이 Executor가 아닌 다른 프로세스에서 동작할 수 있다. 예를 들어, 파이썬으로 데이터 로직을 작성한 경우 슬레이브 서버에서 실행된 일부가 파이썬 프로그램으로 실행된다. 파이썬 인터프리터의 성능이나 파이썬에 동작하는 프로그램과 Executor와 통신이 스칼라나 자바로 데이터 로직을 작성한 경우에 비해 성능이 저하되는 경우가 있다. 

최근 이러한 문제를 해결한 구조로 DateFrame / Dataset이 있다. DateFrame/Dataset은 스파크 컴포넌트 중 하나인 Spark SQL의 기본적인 데이터 구조다. 처리 대상의 데이터를 관계형 데이터베이스의 테이블 형태로 추상화하고 칼럼명과 데이터 형등의 스키마를 부여할 수 있다. 이후 이를 DataSet이라고 나타내겠다. 

프로그래밍 언어의 측면에서는 DateSet이 RDD와 마찬가지로 데이터 처리를 위한 인터페이스로 볼 수 있다. 그러나 RDD와는 달리 개발자는 SQL처리 처럼 Dataset에 대한 쿼리를 작성하여 데이터 처리 애플리케이션을 개발 할 수 있다. 

선언전 API가 있기 때문에 복잡한 처리를 조립하여도 RDD를 기반으로 한 처리보다 확연히 가독성이 좋다. 또한 DataSet을 기반으로 한 처리는 Spark SQL의 옵티마이저에 의해 최적화가 되어 개발 언어에 상관 없이 JVM에서 실행가능한 코드를 생성한다. 이 때문에 수작업에 의한 최적화 수고를 줄일 수 있고, 개발 언어간의 차이에 따라 성능 차이도 나지 않게 된다. 

## Strucutred Streaming

Strucutred Streaming은 스파크를 구성하는 컴포넌트 중 하나로 스트림 처리를 여러 서버에서 병렬로 실행하는데 사용된다. 

Strucutred Streaming은 Spark SQL에서 동작하는 새로운 스트림 처리를 위한 컴포넌트다. Strucutred Streaming은 장애 발생 후 일관된 복구를 위해 설계되었고, 이벤트 타임 윈도 집계 처리 지원등 Spark Streaming에서 어려웠던 기능이 구현되어 있다. Strucutred Streaming은 Dataset을 대상으로 한 컴포넌트이다. 

Strucutred Streaming은 Dateset을 기반하기 때문에 SparkSQL로 배치 처리 애플리케이션을 개발할 때와 동일한 API를 사용해 처리할 수 있다. 따라서 배치 처리와 스트림 처리를 혼용할 경우도 통일된 방법으로 처리를 할 수 있다. 또한 Spark SQL 옵티마이저로 최적화 가능한 장점도 있다. 

### Strucutred Streaming 데이터 처리 모델

대다수 스트림 처리 컴포넌트는 스트림 데이터가 도착한 시점에 처리되는 Event-Driven 방식을 따른다. 반면 Strucutred Streaming은 트리거라 불리는 데이터 처리를 위한 시간을 정의하고 있다. 

트리커는 정기적인 간격으로 반복해서 데이터를 수신하고, 수백 밀리초에서 수초 정도의 짧은 배치를 반복 실행하여 스트림을 처리한다.  이것을 마이크로 배치라고 부른다.

또한 데이터 모델의 특징도 있는데, Strucutred Streaming에서는 데이터 스트림을 RDBMS의 테이블 처럼 취급한다. 그리고 데이터 스트림 안의 데이터를 테이블에 무한히 추가되는 레코드로 취급한다. 이런 가상의 테이블을 입력 테이블(input table)이라 한다.

트리거마다 실행되는 마이크로 배치는 점진적으로 레코드가 추가되는 입력 테이블에 쿼리를 실행해 데이터를 처리한다. 입력 테이블에 쿼리를 실행해 얻는 결과를 결과 테이블이라고 부른다. 

마이크로 배치 1회의 출력은 결과 테이블에 포함된 레코드의 일부거나 전부 일 수 있다. 이 출력 레코드는 출력 모드에 의해 제어 되는데 크게 세가지 종류가 있다. 

* Complete
	* 생성된 결과 테이블에 포함된 레코드를 모두 출력한다.
* Update
	* 이전 트리커에서 생성된 결과를 갱신 또는 추가된 레코드를 출력한다.
* Append
	* 직전 트리거에서 생성된 결과 테이블에 추가된 레코드를 출력한다.

어떤 출력 모드를 설정 할 수 있는지는 쿼리로 표현된 데이터 처리 종류에 따라 다르다. 다음은 각 출력 모드가 설정 가능한 처리의 예이다.

* Complete
	* 집약 처리의 결과 노출시 사용 가능
* Update
	* 컬럼의 선택이나 필터, 집약 처리에 사용 가능
* Append
	* 컬럼의 선택이나 필터, 조인, 워터마크가 설정된 집약 처리

프로그래밍 측면에서는 Dataset이 입력 테이블이나 결과 테이블, 그리고 그 중간 테이블의 인터페이스가 된다. 입력 테이블에 해당하는 DataSet에 대한 쿼리를 작성하면 중간 테이블에 해당하는 Dataset을 얻을 수 있다. 그리고 중간 테이블에 해당하는 Dataset도 마찬가지로 쿼리를 작성해 이를 반복하여 최종으로 얻은 Dataset은 결과 테이블에 해당한다. 또한 입력 테이블에 해당하는 Dataset에서 결과 테이블에 해당하는 Dataset을 얻을 때까지 일련의 쿼리는 통상 마이크로 배치 1회당 1번 실행된다. 

# Spark 애플리케이션 동작 환경

트위터에서 수집한 트윗 데이터를 처리하는 애플리케이션을 만들고 카프카와 Strucutred Streaming을 연계하는 법을 알아보자. 

1. Twitter API를 이용하여 트윗 데이터를 수집하는 Tweet Producer가 존재.
2. Tweet Producer의 JSON 형식의 트윗 데이터를 Kafka의 Tweet 토픽에 송신
3. Apache Spark로 만든 스트림 처리 애플리케이션(Structred Streaming)에서 Tweet 토필에 쌓인 트윗 데이터를 스트림 처리 하고 다시 Kafka processed-tweat 토픽에 송신한다.
4. Kafka Console Consumer에서 processed-tweet 을 경유하여 수신한 트윗 데이터의 처리 결과를 콘솔에 표시

## Spark 설정

스파크와 Strcutured Streaming을 사용하여 개발된 애플리케이션을 동작시키는 프로덕션 환경에는 YARN 등의 클러스터 관리자에서 관리되는 클러스터를 이용하는 것이 일반적이다. 

단 클러스터의 구축 절차는 이 책의 범위를 벗어남으로 작성한 애플리케이션은 클러스터 관리자가 아닌 로컬 모드로 동작한다. 로컬모드는 스파크 애플리케이션을 실행하는 클라이언트가 Driver로 동작해서 여러 스레드를 사용해 클러스터상에서 Executor에 의한 병렬 분산 처리를 애뮬레이트 하는 모드다. 주로 애플리케이션을 개발할때 동작 확인에 사용한다. 

이 책에서는 로컬 모드로 애플리케이션을 동작시킨다는 전재로 설명하지만 작성할 애플리케이션을 클러스터상에서 실제로 병렬 처리시킬 수도 있다. 클러스터 관리자에서 YARN을 사용해 하둡 클러스터에서 애플리케이션을 동작시키는 경우는 나중에 스파크를 하둡과 함께 사용할때의 설정에서 나중에 알아보겠다.

## 카프카 설정

tweet 및 processed-tweet은 카프카 클러스터를 실행한 상태로 kafka-client에서 다음 명령을 실행한다. 

```
// Tweet 토픽 작성
kafka-topics \
--zookeeper kafka-broker01:2181, ...
--create
--topic tweet \
--partitions 1 \
--replication-factor 1

// processed-tweet 토픽 작성
--zookeeper kafka-broker01:2081
--create \
--topic processed-tweet \
--partitions 1
--replication-factor 1 
```

## 아파치 스파크 설정

메타 데이터 디렉터리 작성

Structured Streaming에는 장애 등으로 스트림 처리가 중단된 뒤에도 도중에 재개할 수 있는 복구 법이 있다. 이 방법의 하나로 Strucutrued Streaming에서는 복구에 필요한 메타 데이터를 파일 시스템상에 기록한다. 보통 HDFS와 같이 분산 파일 시스템이 사용되는 것이 일반적이지만 로컬 모드에서는 동작 확인 등을 목적으로 클라이언트의 파일 시스템을 사용하는 것도 가능하다. 이 책에서는 Spark-client가 로컬 파일 시스템을 사용하므로 로컬 파일 시스템의 홈 디렉터리에 메타 데이터 저장용 디렉터리를 작성한다;

## 카프카와 Structured Streaming 연계

앞단에서 트윗 데이터를 kafka에 공급하는 것 까지는 완료되었다고 가정한다. 

### 스파크 셀 실행

스트림 처리 과정에서 필요한 내용을 스파크 셀을 사용한다. 스파크 셀은 스파크에 속한 도구로 스칼라로 코드를 작성하거나 대화식으로 실행하는 셀이다. 매번 코드를 빌드할 필요 없이 프로토 타입 작성 단계에서 테스트가 가능하다. 

```
spark-shell --master local[10]
```

--master local[10] 옵션은 스파크 셀을 로컬 모드로 실행하기 위한 옵션이다. 대괄호 안에 숫자는 에뮬레이트하는 병렬도를 뜻한다. 보다 정확한 의미로는 로컬모드로 에뮬레이트 할때 사용할 스레드의 개수를 말한다. 

### Dataset 생성

Structured Streaming에 의한 스트림 처리는 입력 데이터에 대응한 Dataset을 생성하는 것 부터 시작한다. 처음에는 DataStreamReader이다. DateStreamReader는 SparkSession의 readStream 메서드를 호출하여 얻을 수 있다. 

SparkSession은 Spark SQL의 세션을 나타내는 클래스다. 스파크 셀을 실행햇을때 Spark session available as sparkf에 확인이 가능하다. 스파크 셀은 처음부터 spark라는 인스터스가 생성되어 있다. 

```
val = reader = spark.readStream
val formatConfigured = reader.format("kafka")
```

DataStreamReader에서는 format 메서드로 입력 데이터 형식을 설정할 수 있다. 카프카에서 데이터를 수신하는 경우는 kafka라고 설정한다.

카프카에서 데이터 수신하는 경우 Executor는 브로커에 대해 컨슈머로 동작한다. 컨슈머의 동작은 DataStreamReader의 option을 사용하여 제어할 수 있다. 

일반적인 컨슈머에서는 처음에 접속할 브로커의 목록, Group ID, 레코드 Key와 Value의 디시리얼라이저의 설정이 필수 였지만, StructuredStreaming에서는 브로커 목록만이 필수설정이다. 

데이터 수신원이 되는 토픽을 설정하기 위한 옵션은 아래와 같다.

|옵션| 설정값 형식  | 예|
|--|--|--|
| assign| JSON 형식으로 토픽과 파티션 번호 목록을 쌍으로 열거한다.| { "topic1":[0,1,2], "topic2": [3,4]} |
| subscribe| 데이터 수신원이 되는 토픽을 쉼표로 구분하여 열거| topic2, topic3 |
| subscribePattern| 자바의 정규 표현식 문법으로 수신원이 되는 토픽 패턴을 작성| topic[1-3]|

format 메서드는 DateStreamReader 인스턴스 자신을 반환하기 때문에 계속해서 option 메서드를 활용하여 설정이 가능하다. 

아래처럼 tweet 트윅 데이터를 가져오도록 subscribe 옵션을 설정해보자.

```
val = optionConfigured = formatConfigured.option("subscribe", "tweet")
.option("kafka.bootstrap.servers", "kafka-broker01:9092, ..." )
```

참고로 여러 옵션을 체인 메서드 형태로 호출하는 것 외에도 Map 형태로 담아 보내는 것도 가능하다. 

포맷이나 옵션이 설정된 DataStreamReader에서 load 메서드를 호출하면 Dataset이 생성된다. 

생성된 Dataset에는 자동적으로 스키마가 부여되어 있다. 스키마는 Dataset의 printSchema 메서드로 확인하고 있다. 

```
val tweetDS = optionConfigured.load
tweetDS.printSchema
```

부여된 스키마는 format 메서드에서 설정한 포멧에 따라 다르지만 아래 스키마가 부여되어 있다. 

|컬럼| 데이터 형  | 의미|
|--|--|--|
| key | binary | Key로 설정된 데이터|
| value | binary | Value로 설정된 데이터|
| topic | string | 토픽명 |
| partition | int | 파티션번호 |
| timestamp | long | 타임스탬프 |
| timestamType | int | timestamp 컬럼에 설정된 값을 나타내는 타임 스탬프 종류에 따라 다음과 결정하다 |

카프카에서 수신한 데이터 key, value는 Date의 key 컬럼과 value 컬럼의 이진형으로 저장된다. 작성한 트윗 프로듀서에서는 트윗 데이터를 카프카에 송신하는 데이터의 Value로 표현했다. 송신된 트윗 데이터는 tweetDS의 value의 컬럼에 보관되다. 

## 쿼리 작성

생성된 Dataset에 대해서는 Spark SQL이 제공하는 API를 이용하여 쿼리를 작성할 수 있다. 이러한 API는 selectExpr이나 filter 등, SQL 구문과 유사한 메서드 군이 포함되어 있다. 

원래  트윗 데이터를 가공하는 쿼리 작성의 예를 소개하겠다. tweetDS의 value 컬럼에는 JSON 형식의 데이터가 저장된다. 그리고 JSON 각 필드는 트윗의 속성을 표현하고 있다. 

예를 들어, 트윗 데이터에는 retweeted_status 속성에 포함된 것이 있는데, 이 속성이 포함된 트윗 데이터는 다른 트윗의 리트윗을 나타낸다. 또한 리트윗된 원래 트윗 데이터의 각종 속성은 retweeted_status 속성 값이 중첩된 방식으로 표현되어 있다. 

좋아요 횟수가 1000이상이 트윗 데이터를 추출하는 쿼리를 작성해보자. 

첫 번째 단계에서는 selectExpr를 이용해서 selectedDS를 생성한다. selectExpr은 SQL의 SELECT 구문에 해당하는 처리를 하는 메서드로, 투영의 원본이 되는 Dataset의 컬럼을 지정하거나 식을 작성할 수 있다. 트윗 데이터의 각종 속성은 JSON 필드로 value 컬럼에 저장된다. 

Spark SQL에서는 SQL과 마찬가지로 많은 내장 연산자와 함수가 제공된다. 그리고 JSON 형식의 문자열에서 특정 필드를 추출하기 위해서는 get_json_object 함수를 사용할 수 있다. 

여러가지를 바탕으로 코드를 짜면 아래와 같다. 

```
val selectedDs = tweetDS.selectExpr("CAST(value AS string) AS value_as_str",
.selectExpr("Cast(get_json_object)"),
...
```

이렇게 얻어진 selectedDs 스키마는 아래와 같다. 
selectExpr에 의해 새로운 Dataset이 생성되었다. 이렇게 메서드를 호출할때마다 생성되는 Dataset은 입력 테이블에서 결과 테이블을 얻기까지 중간 테이블을 추상화 한것이다. 

이어서 두번째 단계를 실시할 쿼리를 작성한다.  DataFrame안의 레코드 중에 특정 조건에 일치하는 것만 추출하려면 filter 또는 where 조건을 사용한다. filter는 SQL의 WHERE 구문과 유사하며, Dataset안의 컬럼등을 피연산자로 해서 조건식을 기술함으로써 조건에 일치하는 레코드를 추출한 Dataset을 생성한다. 

앞 단의 selectExpr에서 AS 연산자를 사용하여 각 컬럼에 별칭을 부여했다. 따라서 filter에 기술하는 조건식의 피연산자는 별칭으로 참조할 수 있다. 

```
val filteredDS = selectedDS.filter("retweet_count >= 1000 AND favortie_count >= 1000")

```




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0ODQzMTQyNCwxMTE4MTc2MDExLC0xOT
k2NjIxNTc5LC03NzI0OTA2MiwxNjA1OTU2MjI5LDMwMjA1NjIx
NCwtNTkxNjAwNDUzLC0yMDg4NzkwMTI0LC0xNzc2NzQ3ODg1LC
0xNzA1NjM2NTA0LC00MzM1NTMxMjAsMTgzMTEwNDE2NywyMDA0
NDg3MDcxLC0xMTMzOTE2NTcyLC0xMzk1ODkwNjc4LDgwOTA0OD
E4NiwtMjExMjQxMTQ0NSwtMTYwMDgxNzQ0NCwtNjUzMjg2MzM1
LDIzNDExODM1NF19
-->