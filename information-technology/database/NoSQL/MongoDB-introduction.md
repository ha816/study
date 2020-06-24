# MongoDB  

MongoDB는 [NoSQL](https://namu.wiki/w/NoSQL "NoSQL")  [DBMS](https://namu.wiki/w/DBMS "DBMS")의 한 종류입니다.

MongoDB는 오픈소스 문서지향(Document-Oriented)적 Cross-platform 데이터베이스이며, 뛰어난 확장성과 성능을 자랑합니다. 또한, 현존하는 NoSQL 데이터베이스 중 인지도 1위를 유지하고있습니다.

[MySQL](https://namu.wiki/w/MySQL "MySQL") 처럼 전통적인 테이블-관계 기반의 [RDBMS](https://namu.wiki/w/RDBMS "RDBMS")가 아니며 [SQL](https://namu.wiki/w/SQL "SQL")을 사용하지 않습니다. 참고로 mongo는 humongous의 줄임말이라고 합니다. 즉 거대한 DB라는 뜻입니다.

# Component

## Document

Document는 RDMS의 record, row에 대응하는 데이터 구조입니다. 한 개이상의  key-value pair 로 구성됩니다.

아래 Document 예제를 한번 봅시다.

{
    "id": ObjectId("5099803df3f4948bd2f98391"),
    "username": "velopert",
    "name": { first: "M.J.", last: "Kim" }
}

여기서 id, username, name 은 key이고 대응하는 오른쪽에 값들은 value 입니다.

id 값은 12bytes의 hexadecimal 값으로서, 각 document의 유일함(uniqueness)을 제공합니다.  
첫 4bytes는 현재 timestamp, 다음 3bytes는 machine id, 다음 2bytes는 MongoDB 서버의 프로세스id, 마지막 3bytes는 순차번호입니다.

Document는 기본적으로 추가될때마다 값이 높아집니다.

Document는 동적(dynamic) schema입니다. 즉, 같은 Collection 안에 있는 Document 임에도 서로 다른 schema를 갖고 있을 수 있습니다.

## Collection

Collection은 Document의 집합입니다. RDMS측에 비유하자면, Table에 대응합니다. 하지만 RDMS와 달리 schema를 따로 가지고 있지 않습니다.

## Database

Database는 Collection들의 물리적인 컨테이너입니다. 각 Database는 파일 시스템에 맞게 최적으로 저장됩니다.

# BSON

BSON은 간단히 말하자면 "Binary JSON"입니다. BSON의 이진 구조는 타입과 길이 정보를 인코드하여 훨씬 빠르게 파싱되도록 합니다.

date와 binary 데이터와 같은 JSON-native가 아닌 데이터 타입을 추가함으로써, MongoDB가 이런 타입도 지원이 가능하다. 심지어 어떤 복잡한 형태의 수학적인 데이터도 지원합니다. 

## Does MongoDB use BSON, or JSON?

MongoDB는 내부적으로 또 네트워크 상으로  BSON 형태로 데이터를 저장합니다. 
그러다 보니 MongoDB를 JSON Database가 아니라고 생각할 수 있지만 이는 사실이 아닙니다. 
JSON으로 표현 가능한 어떤것도 MongoDB로 자연스럽게 저장 가능하고, 또 JSON으로 찾을 수 있습니다.  

```groovy
{"hello": "world"} 
→ 
\x16\x00\x00\x00           // total document size
\x02                       // 0x02 = type String
hello\x00                  // field name
\x06\x00\x00\x00world\x00  // field value
\x00                       // 0x00 = type EOO ('end of object')
```

JSON을 그대로 사용하는 다른 시스템과는 다르게, MongoDB는 BSON을 이용해서 강력한 인덱스와 쿼리  특성을 제공합니다. 여기서 쿼리 특성이라 하면, 찾거나 수정할 Objects를 JSON/BSON 문서내의 특정 키로 찾을 수 있습니다. 심지어 다수의 레이어로 중첩된 문서에 대해서도 가능합니다.

## JSON vs BSON

|| JSON | BSON|
|--|--|--|
|Encoding    | UTF-8 String| Binary| 
|Data Support|String, Boolean, Number, Array|String, Boolean, Number (Integer, Float, Long, Decimal128...), Array, Date, Raw Binary|
|Readability| Human and Machine|Machine|

사실 JSON과 BSON은 디자인적으로 매우 가까운 사촌지간이다. BSON은 JSON 데이터의 이진 표현이며 단지 애플리케이션을 위해 추가 확장과 데이터 저장과 검색을 위해 최적화가 되었습니다. 

BSON이 JSON가 다른 점 한가지는 BSON이 더욱 진보된 형태의 데이터 타입을 지원한다는 것입니다. 예를들어, 두 정수간의 차이 그리고 실수간의 차이를 계산할 수 있습니다.

대다수 서버측 프로그래밍 언어는 훨씬 복잡한 숫자형 타입을 가지고 있습니다 (integer, floating point, double 등등), 각 숫자형 타입은 효과적인 수학적 연산을 위해 고유한 최적의 사용법을 가지고 있습니다.

## BSON Extended Types

### ObjectId

ObjectIds 작고, 빠르게 생성하고, 거의 unique하고 순서가 정해집니다. ObjectId 값은 12bytes 길이로 이 12 bytes으 아래로 구성됩니다.

-   a 4-byte  _timestamp value_, representing the ObjectId’s creation, measured in seconds since the Unix epoch
-   a 5-byte  _random value_
-   a 3-byte  _incrementing counter_, initialized to a random value

While the BSON format itself is little-endian, the  _timestamp_  and  _counter_  values are big-endian, with the most significant bytes appearing first in the byte sequence.

MongoDB 클라이언트는 `_id` 필드를 유일한 ObjectId 로 추가해야 합니다. ObjectIds를 사용하는 것은 아래와 같은 이점들이 있습니다.

* mongo shell에서,  `ObjectId.getTimestamp()` 메서드를 사용하면 ObjectId의 생성 시간을 알 수 있습니다. 
* ObjectId를 저장하면 `_id`필드가 거의 생성 시간으로 정렬된 것과 같습니다. 

> objectid값이 시간에 따라 커지는 것은 일반적으로 맞지만, 필수불가결하게 단조롭게 모든 objectid값이 항상 커지는 것은 아닙니다. 왜냐하면 같은 초에 생성된 값은 순서를 보장하지 않고, 클라이언트가 objectId를 만들어 시스템 시간이 다를지도 모르기 때문입니다.   

### String 

BSON 문자열은 UTF-8을 따릅니다. 

일반적으로, 프로그래밍 언어의 각 드라이버는 BSON으로 직렬화나 역직렬화를 할때 언어의 문자열 포맷을 UTF-8로 변경합니다. 때문에 대부분의 BSON 문자열의 각 문자들을 저장하는게 쉽습니다. 게다가 MongoDB는 $regex 쿼리로 regex 문자열의  UTF-8을 지원합니다. 

UTF-8 문자들을 사용한 문자열에 sort()를 수행하면 자연스럽게 잘 동작합니다. 그러나 sort() 내부적으로 C++ strcmp()를 사용하는데, 몇몇 문자는 잘못 다룰 수도 있습니다. 

### TimeStamp

BSON은 MongoDB 내부를 위해 특별한 timestamp 타입을 가지고 있습니다. 정규 Date 타입과는 관련이 없습니다. timestamp 타입은 64bit 값을 가지고 그 구성은 아래와 같습니다.

* 중요한 32bit는 time_t 값입니다.(Unix epoch 이후 흐른 seconds)
* 나머지 32bit는 주어진 시간안의 연산들의 증가하는 순서를 나타냅니다.

BSON 포맷이 little-endian이기 때문에 나머지 bits를 먼저 저장하는 반면에, mongo daemon 객체는 endian 형태를 생각치 않고, 항상 모든 플랫폼에 대한 `ordianl` 값 전에 `time_t` 값을 비교합니다.  

단일 mongo daemon 안에서, timestamp 값은 항상 unique합니다. 

> 주의할점
> BSON timestmp는 내부 MongoDB용으로 사용됩니다. 따라서 대부분의 애플리케이션 개발에서, BSON Date type을 사용하게 될것 입니다. 

timestamp 값이 빈 상태인 top-level 문서를 삽입하면, MongoDB가 빈 timestamp 값을 현재 timestamp 값으로 대체합니다. 예외로, `_id` 필드 스스로가 timestamp가 없다면, 없는 상태로 들어가며 대체되지 않습니다. 

### Date

BSON Date는 64bit 정수형으로 Unix epoch (Jan 1, 1970) 이후로 milliseconds의 수를 나타냅니다. 
공식적인 BSON Date 타입은 UTC datetime입니다. 
BSON은 양수 값을 가지며, 음수값의 경우는 1970년 이전을 나타냅니다. 


    
# Operations

`launchd` 런치를 위한 데몬에게 `mongod` 몽고 DB를 위한 데몬을 시작 또는 다시 시작하게 하고 싶다면 아래 명령어를 사용합시다. 

```
brew services start mongodb-community
```
반대로 서버 인스턴스를 멈추고 싶다면 아래 명령어를 수행합시다. 
```
brew services stop mongodb-community
```



<!--stackedit_data:
eyJoaXN0b3J5IjpbNDYyNzc1NzgzXX0=
-->