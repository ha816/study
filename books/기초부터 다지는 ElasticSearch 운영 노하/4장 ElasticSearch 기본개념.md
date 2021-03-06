# Overview

이번 장에서는 ES를 구성하는 기본 개념들에 대해서 정리해보겠다. 

* 클러스터와 노드의 의미
* 인덱스와 타입의 의미
* 샤드와 세그먼트의 의미
* 매핑의 역할과 의미

# 클러스터와 노드의 개념

먼저 클러스터와 노드에 대해서 살펴보자. 클러스터란 여러 대의 컴퓨터 혹은 구성요소들을 논리적으로 결합하여 전체를 하나의 컴퓨터 혹은 하나의 구성요소처럼 사용할 수 있게 해주는 기술이다. 마찬가지로 ElasticSearch 클러스터 역시 여러 개의 ES 프로세스들을 논리적으로 결합하여 하나의 ES 프로세스처럼 사용할 수 있게 해준다.

이러한 클러스터를 구성하는 하나하나의 ES 프로세스를 노드라고 부른다. 즉, 여러 개의 ES 노드들을 마치 하나의 ES로 동작하게 하는 것이 ES 클러스터라고 할 수 있다. 

클러스터는 하나 이상의 노드로 구성된다. 클러스터가 3개의 노드로 구성되어 있다고 하자. 3개의 노드가 마치 하나의 ES처럼 동작하기 때문에 사실 3개의 노드 중 어떤 노드에 API를 요청해도 동일한 응답과 동작을 보장받는다. 

클러스터가 단일 노드로 구성된 경우에도 클러스터처럼 동작하기 때문에 정상적인 응답과 동작을 기대할 수 있다.

다수의 노드로 구성된 ES 클러스터는 고유의 클러스터 이름과 UUID(Universally Unique Identifier)를 가진다. 그리고 이 두 가지 고유한 속성을 통해 클러스터 내에 속한 노드들이 서로 동일한 클러스터 내에 있음을 인식하고 클러스터링 된다. 


curl 명령을 통해서 각각의 노드에서 해당 노드가 속한 클러스터의 정보를 확인할 수 있다. 

```
curl localhost:9200
{
	"name" : "elasticsearch",
	"cluster_name" : "my-elasticsearch",
	"cluster_uuid" : "680Dns66Rpkencwewe",
	"version" : {
		...
	},
	...
}
```

name은 클러스터에 속한 노드들 중 현재 요청에 응답한 노드의 이름이다. 클러스터가 다수의 노드로 구성된다면 노드별로 name값은 달라질 수 있다. 현재는 단일 노드이다. 

cluster_name은 클러스터의 이름이다. 클러스터에 속한 모든 노드들은 클러스터 이름이 같다. 새로운 노드를 클러스터에 추가하기 위해선 반드시 같은 클러스터 이름을 사용해야 한다. 만약 이름이 다른 노드가 클러스터에 합류하려고 하면 에러가 발생한다. 

cluster_uuid는 클러스터의 UUID이다. 이 값 역시 클러스터에 속한 모든 노드가 동일한 값을 가진다. 이 값은 클러스터가 최초 생성될때 자등으로 생성되는 것으로 사용자가 정하는 값은 아니다.

## 노드의 역할

노드는 클러스터를 구성하는 논리적인 ES 프로세스 하나를 의미한다. 노드도 클러스터와 마찬가지로 각각의 고유한 노드 이름과 UUID가 있고, 역할에 따라 여러 가지 노드로 구분할 수 있다. 


| 노드역할| 설명 |
|--|--|
| 마스터  | 클러스터 구성에서 중심이 되는 노드. 클러스터의 상태 등 메타데이터를 관리한다.|
| 데이터  | 사용자의 문서를 실제로 저장하는 노드 |
| 인제스트 | 사용자의 문서가 저장되기 전 문서 내용을 사전 처리하는 노드|
| 코디네이트 | 사용자의 요청을 데이터 노드로 전달하고, 다시 데이터 노드로부터 결과를 취합하는 노드|

노드가 가질 수 있는 역할은 총 4가지 이며, 각각 하나의 역할만을 하는게 아니라 한번에 여러 역할을 겸할 수 있다. 각각의 역할을 좀더 자세히 알아보자. 

### 마스터

마스터 노드는 클러스터의 메타 데이터를 관리하는 역할을 한다. 그리고 클러스터내에서 반드시 한대 이상이 있어야 한다. 

클러스터 안의 모든 노드는 현재 노드의 상태, 성능 정보, 자신이 가지고 있는 샤드의 정보를 마스터 노드에 알린다. 마스터 노드를 이런 정보를 수집하고 관리하면서 클러스터의 안정성 확보를 위한 작업을 수행한다. 

### 데이터 노드 

데이터 노드는 사용자가 색인한 문서를 저장하고, 검색 요청을 처리하여 결과를 반환하는 역할을 한다. 

상황에 따라 자신이 받은 요청 중 자신이 처리할 수 있는 요청은 직접 처리하고, 다른 데이터 노드들이 처리해야할 요청은 해당 데이터 노드에 전달한다. 

이때 어떤 노드로 요청을 전달할 것인지는 마스터 노드를 통해 받은 클러스터의 전체 상태 정보를 바탕으로 한다. 

### 인제스트 노드

인제스트 노드는 사용자가 색인하길 원하는 문서와 내용 중 변환이 필요한 부분을 사전에 처리한다. 데이터 노드에 저장하기 전에 특정 필드의 값을 가공해야 할 경우 유용하게 동작한다. 

(인제스트는 섭취하다. 받아들이다라는 뜻을 가진다.)

### 코디네이트 노드

코디네이트 노드는 실제 데이터를 저장하고 처리하지는 않지만, 사용자의 색인이나 검색 등 모든 요청을 데이터 노드에 전달하는 전달자 역할을 한다. 문서를 저장하지 않는 데이터 노드라고 생각할 수도 있다.

## 유의사항

클러스터 내에서 메타 데이터를 관리하는 마스터 노드는 반드시 한대다. 마스터 노드는 마스터의 역할이 가능한 노드와 실제 마스터 역하을 하는 노드 두 가지를 모두 통칭하는 말이다. 

만약 클러스터 구축시 3대의 마스터 노드로 구성한다면 그 중 실제론 한 대만 실제 메타데이터를 관리하는 역할을 수행한다. 나머지는 현재 동작하고 있는 마스터 노드에 장애가 발생했을때 새로운 마스터가 될 수 있는 마스터 후보 노드가 된다. 

마스터 후보 노드들은 마스터 노드들로 부터 지속적으로 클러스터 운영을 위한 데이터를 전달받기 때문에 항상 같은 메타 데이터를 유지한다. 그래서 마스터 노드에 장애가 발생하여 새로운 마스터 노드가 되도 중단 없이 서비스를 이어갈 수 있다. 

위에서도 언급했지만 각 노드들은 하나의 노드 역할만 수행하는게 아니라 동시에 다양한 역할을 수행할 수 있다. 

예를 들어, 한 노드에 아래와 같이 다양한 동시에 부여할 수 있다.
노드1 : 마스터 + 코디네이터
노드2 : 데이터 + 인제스트 + 코디네이트 

# 인덱스와 타입 

인덱스는 사용자의 데이터가 저장되는 논리적인 공간을 의미하며 타입은 인덱스 안의 데이터를 유형별로 논리적으로 나눠 놓은 공간을 의미한다. 보통 인덱스와 타입에 대해 이야기 할때 RDBMS와 비교하기도 한다. 

ES에서 인덱스는 RDBMS에서 데이터베이스, 타입은 테이블과 비슷한 개념이다. 하지만 RDBMS에서는 데이터베이스 안에 여러 개의 테이블을 가질 수 있는 것과 달리 ES는 6버전 이후로 반드시 하나의 인덱스에 하나의 타입만을 가질 수 있다. 

nginx 웹서버에서 발생하는 접속 로그를 수집한다고 가정해보자. 먼저 접속 로그를 저장할 인덱스가 필요할 것이다. 인덱스의 이름은 자유롭게 만들 수 있지만 nginx-access-log-2019.05.07과 같이 의미 있는 이름으로 짓는것이 해당 인덱스에 있는 로그 데이터들의 생성날짜, 유형등을 짐작 할 수 있어 나중에 정보 파악에 유리하다. 

접속로그에 해당 인덱스에 쌓게 될 문서들의 타입명이 필요할 것이다. ES 6이후부터는 단일 타입만 허용하기 때문에 큰 이슈가 없다면 _doc이라는 타입명으로 저장된다. 

사용자에게 영화와 책에 대한 검색 엔진을 구축한다고 가정하면 각각의 문서를 저장할 인덱스가 필요할 것이다. 영화와 책은 서로 다른 형태의 JSON 문서를 가지게 되기 때문에 인덱스도 분리하는 것이 좋다. 영화와 관련된 movies 인덱스에,  책과 관련된 문서는 books인덱스에 저장한다. 마찬가지로 타입도 _doc을 사용하게 된다. 

인덱스의 이름은 클러스터 내에서 유일해야 하며, 동일한 이름의 다른 인덱스를 만들 수는 없다. 인덱스내에 저장된 문서들은 데이터 노드들에 분산되어 저장된다. 

## 멀티타입(Deprecated)

ES 버전 6 이상 버전은 하나의 인덱스에 하나의 타입만을 사용하도록 강제하고 있지만, 과거에는 다수의 타입을 사용할 수 있었다. 

그러면 왜 6버전부터는 멀티타입을 허용하지 않게 됬을까? 
그 이유 중하나는 인덱스에 존재하는 서로 다른 타입에서 동일한 이름의 JSON 필드를 만들 수 있어서 의도치 않은 검색 결과가 나타나는 현상이 있었기 때문이다. 

예를 들어 test_index라는 인덱스에 test_type1, test_type2라는 타입을 만들고 아래와 같은 문서를 각각 하나씩 색인했다고 가정하자. 
```
{
	"name": "elasticsearch",
	"type_name": "test_type1",
}
{
	"name": "elasticsearch",
	"type_name": "test_type2",
}
```
그러면 두 문서는 서로 다른 타입이지만 name이라는 같은 필드를 가지게 된다. 그리고 아래와 같은 쿼리를 test_index에 요청해보자.

```
curl -XGET "http://localhost:9200/test_index/_search?q=name:elasticsearch&pretty"
{
...
	"hits" : [
		"total" : 2,
		"max_score" : 0.28...
		"hits" : [
			{
			"_index" : "test_index",
			"_type" : "test_type1",
			"_id" : "abcde", 
			},
			{
			"_index" : "test_index",
			"_type" : "test_type2",
			"_id" : "abcde", 
			}
		]
	]
}
```
ES에 검색시 name필드로 elasticsearch에 해당하는  문서를 검색했다. test_type1, test_type2 문서가 모두 검색된다. 이러한 문제 때문에 6버전부터는 하나의 인덱스에 하나의 타입만 지원하게 되었다.

ES에서는 _doc이라는 이름으로 타입을 사용하도록 권고하고 있으며, 이후에는 _doc이라는 이름으로 고정될 예정이다. 참고로 5버전 이하에서는 _ 언더바로 타입을 생성할 수 없다. 

# 샤드와 세그먼트

샤드는 인덱스에 색인되는 문서들이 저장되는 논리적인 공간을 의미한다. 즉 샤드는 논리적인 공간이기 때문에 실제 물리적으로는 각 데이터 노드에 나누어져 저장될 것이다.

세그먼트는 색인된 문서들의 집합으로 실제 물리적인 파일이다. 하나의 인덱스는 다수의 샤드로 구성되고 하나의 샤드는 다수의 세그먼트로 구성된다. 샤드는 1개 이상의 세그먼트로 구성되는데 샤드마다 가지는 세그먼트의 개수는 서로 다룰 수 있다. 

![enter image description here](https://leonlibraries.github.io/2017/04/27/ElasticSearch%E5%86%85%E9%83%A8%E6%9C%BA%E5%88%B6%E6%B5%85%E6%9E%90%E4%B8%89/segments.jpg)

만약 5개의 샤드로 구성된 인덱스라면 실제 문서는 아래와 같이 각각의 샤드에 나누어 저장된다. 

|인덱스|
|--|
| 샤드0{"no":1, "id":1230, "name":"Park"}|
| 샤드1{"no":2, "id":4235, "name":"kim"}|
| 샤드2{"no":3, "id":2358, "name":"lee"}|
| 샤드3{"no":4, "id":4444, "name":"kang"}|
| 샤드4{"no":5, "id":5555, "name":"lee"}|

만약에 위 3번 샤드에서 문제가 발생된다면, 샤드 3에 저장된 문서들은 검색할 수 없거나 색인되지 않는 등의 문제가 생긴다. 문서들이 인덱스 내에 저장된다는 개념을 정확히 이해하려면 이런 장애가 발생했을때 장애의 규모를 정확하게 파악할 수 있다. 

만약 샤드 0에 장애가 발생하면 사드 0에 저장된 문서에는 접근할 수 없게 되고, 심각하게는 문서가 유실될 수도 있다. 이러한 최악의 상황을 방지하기 위해 데이터를 한번더 복제해서 레플리카 샤드를 만들어 데이터의 안정성을 보장한다. 레플리카 샤드와 일반적인 프라이머리 샤드에 대해선 후에 다시 자세히 다루겠다. 

인덱스에 저장되는 문서는 해시 알고리즘에 의해서 샤드들에 분산 저장되고 이 문서들은 실제로는 세그먼트라는 물리적 파일에 저장된다. 하지만 문서가 처음부터 세그먼트에 저장되는 것은 아니다. 색인된 문서는 먼저 시스템의 메모리 버퍼 캐시에 저장되는데 이 단계에서는 문서가 검색 되지 않는다. 이후 ES의 refresh 과정을 거쳐서 디스크에 세그먼트 단위로 문서가 저장되고 해당 문서의 검색이 가능해진다. refresh에 대해선 10장 색인 성능 최적화에서 자세히 다루겠다.

## 세그먼트 병합

세그먼트는 불변의 특성을 갖는다. 즉 ES에서 문서를 수정하려고 하면 새로운 세그먼트에 수정된 문서를 저장하고, 기존 문서는 불용 처리한다. 이 동작은 delete도 마찬가지인데 불용처리만 한다. 

문서 수정을 위해서 매번 새로운 세그먼트를 생성하면, 시간이 지나면서 작은 크기의 세그먼트가 늘어나게 된다. 이는 사용자가 문서를 검색할때 마다 많은 수의 세그먼트들을 검색하게 되어 검색 성능이 떨어지게 된다. 심지어 불용 처리한 데이터들도 사실 세그먼트에 남아있기 때문에 결과적으로 세그먼트의 전체 크기도 점점 커지게 된다. 

이런 문제를 보완하기 위해 ES에서는 세그먼트 병합을 진행한다. 세그먼트 병합은 백그라운드로 발생하는 병합 작업으로, 여러 작은 세그먼트를 합쳐 큰 세그먼트로 만든다. 동시에 불용처리된 세그먼트는 병합에서 제외되고 실제 디스크에서도 삭제된다. 

# 프라이머리 샤드와 레플리카 샤드

프라이머리 샤드는 최초 인덱스를 생성할 때 사용할 샤드 개수를 결정하는데, 이때 결정한 프라이머리 샤드의 개수는 이후에 변경할 수 없다. 따라서 인덱스를 초기에 생성할때, 몇개의 프라이머리 샤드를 생성할지 신중하게 결정해야 한다. 하지만 아쉽게도 프라이머리 샤드의 개수를 결정하는 것은 정해져 있는 방법이 있는 것이 아니고 ES 사용 용도에 따라 천차만별이기 때문에 굉장히 어려운 작업이다. 이것은 12장에서 다루게 될 것이다. 

ES는 인덱스를 샤드로 나누고 나뉘어진 각 샤드에 세그먼트 단위로 문서를 저장한다. 샤드의 상태를 정상적으로 유지하고 장애 상황에서도 유실되지 않게 하는것은 꼭 필요한 작업이다. 이런 클러스터 서비스의 연속성을 유지하기 위해 샤드를 프라이머리 샤드와 레플리카 샤드로 나누어 관리한다. 

레플리카 샤드는 프라이머리 샤드와 동일한 문서를 가진다. 때문에 사용자의 검색 요청에도 응답이 가능하다. 따라서 레플리카 샤드를 늘리면 응답 속도를 높일 수 있다. 

먼저 프라이머리 샤드를 살펴보자. 기본적으로 프라이머리 샤드는 5개의 디폴트 값을 가진다. 인덱스가 5개의 프라이머리 샤드로 구성되고 나면, 사용자의 문서는 각 프라이머리 샤드에 분산되어 저장된다. 

각 프라이머리 샤드는 구성될때 샤드 번호를 부여받는데 기본적으로 0번 부터 순차적으로 받는다. 그렇다면 문서는 어떤 기준으로 샤드의 번호를 할당받을까? 바로 해시함수이다. 

```
할당된 프라이머리 샤드번호 = hash(문서 Id) % 프라이머리 샤드 개수
```

그리고 바로 이 해시함수 때문에 프라이머리 샤드 개수를 변경할 수가 없다. 만약 프라이머리 샤드 개수가 변경되어야 한다면, 지금까지 저장된 문서의 프라이머리 샤드 번호가 모두 변경되어야 하기 때문이다. 

## 장애복구

특정 노드에서 장애가 발생하여 샤드가 클러스터에서 이탈하면 레플리카 샤드를 사용하게 된다. 레플리카 샤드는 프라이머리 샤드의 복제본으로, 프라이머리 샤드가 저장되니 서버 노드와는 다른 노드에 저장된다. 당연한 이야기지만 노드 한대가 장애가 났을때 복제본도 동일한 노드에 있다면 복제의 의미가 없기 때문이다. 

프라이머리 샤드의 복제본인 레플리카 샤드는 데이터 노드별로 원본과 복제본 샤드의 번호가 중복되지 않도록 할당한다. 덕분에 특정 노드가 클러스터에서 이탈되어도 그 노드에 있던 샤드의 레플리카나 프라이머리 샤드가 다른 노드에 존재하기 때문에 서비스에 문제가 없게 된다. 다른 노드에 존재하던 레플리카 샤드는 곧 프라이머리 샤드로 승격되고, 이것의 또 다른 레플리카 샤드가 다른 노드에 새롭게 생성되며 복구가 진행된다. 

사용자가 따로 설정하지 않는다면 각 프라이머리 샤드당 하나의 레플리케이션 샤드를 만들도록 기본설정이 되어 있다. 변경하고 싶다면 검색하여 찾아보자. 

레플리카 샤드의 갯수는 운영중에도 변경이 가능한점을 기억하자. 샤드의 개수는 데이터의 안정성 측면 뿐만 아니라 색인과 검색 성능 확보면에서도 중요하다. 

# 매핑

매핑은 RDBMS과 비교하자면 스키마와 유사하다. 스키마란 ES에 저장될 JSON 문서들이 어떤 키와 어떤 형태의 값을 가지고 있는지 정의한 것이다. 

사실 매핑 정보는 미리정의해도 되고 정의하지 않아도 된다. 미리 정의하는 것을 정적 매핑(static mapping)이라 하고 정의하지 않는 것은 동적 매핑(dynamic mapping)이라 한다.

동적 매핑은 미리 스키마를 정의하지 않은 상태에서 최초에 색인된 문서를 바탕으로 자동으로 매핑을 생성해주는 방식으로 높은 편의성을 제공한다. 두 방식의 자세한 차이점은 10장의 색인 성능 최적화에서 알아보자. 

매핑이 생성된 이후로 생성되는 문서는 자연스럽게 기존 매핑 정보에 따라 색인되어야 한다. 예를 들어, long 데이터 타입으로 매핑이 생성된 필드는 문자열 데이터가 들어오면 색인되지 않는다. 

다음은 ES에서 사용 가능한 필드 데이터 종류이다.

|데이터 타입| 설명 | 종류|
|--|--|--|
| String  | 문자열 데이터 타입  |text, keyword |
| Numeric  | 숫자형 데이터 타입  |long, integer, short, byte, double, float, half_float, scaled_float|
| Date  | 날짜형 데이터 타입  | date|
| Boolean  | 불 데이터 타입  | boolean|
| Binary  | 바이너리 데이터 타입  | binary|
| Range  | 범주 데이터 타입  | integer_range, float_range, long_range, double_range, date_range|

위의 필드 내용에 따라 지정할 수 있는 필드 데이터타입이 다르며, 같은 종류의 데이터라도 여러 필드 타입이 존재한다. 

예를 들어, 문자열을 색인하더라도 text 타입을 정의할 수 있고, keyword 타입으로 정의할 수도 있다. 이처럼 사용자가 색인한 문서의 다양한 필드들을 적절한 타입으로 스키마를 정의하는 것이 매핑이다. 










> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNDIzOTAxOTgsODIzNTY5MzI5LDY3Mz
A4NjMwOCw4MDg5MTc5MiwtMjEzNzg1MjIyNiwtNzYyOTgyNjU4
LDExMzQ3MTY4NDcsMTEzNTE5NTI5OSwyMTA1Nzc4MDk5LDEwND
gyODY2MzEsLTE4ODkwODY0MTYsMjA0NTk3NDMxLDkwMTQ0OTcw
OSw2NDQ3MzE2ODUsLTYwMzg5OTEwNSwtMTk5MjQ1MjMwMSwxMz
U5MzEyMjc4LDYxNzk5MjIxOSwtNTcxMzA4NTE5LDMyMTc1Mzg3
MV19
-->