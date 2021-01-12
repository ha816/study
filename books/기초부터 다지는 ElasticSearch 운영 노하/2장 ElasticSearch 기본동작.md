# Overview

1장에선 ElasticSearch를 설치하는 법과 curl 명령어로 실행되는지 확인하는 법을 알아보앗다. 이번 장에서는 curl 명령어로 ES의 기본동작들을 살펴보자. 

* 문서를 색인하고 조회, 삭제하는법
* 인덱스를 생성하고 삭제하는 법
* 문서를 검색하고 문서의 내용을 분석하는 법

# 문서 색인과 조회

ElasticSearch는 JSON형태의 문서를 저장할 수 있으며 스키마리스이기 때문에 문서를 색인하기 위해 정형화된 문서의 스키마를 미리 정의할 필요가 없다. 

```
curl -X PUT "localhost:9200/user/_doc/1?pretty" -H 'Content-Type: application/json' -d
{
	"username" : "alden.kang"
}
{
	"_index" : "user",
	"_type" : "_doc",
	"_id" : "1",
	"_version" : 1,
	"result" : "created",
	"_shards" : {
		"total" : 2,
		"successful" : 1,
		"failed" : 0
	},
	"_seq_no" : 0,
	"_primary_term" : 1
}
```

위 curl을 딱 한줄의 명령이지만, ES 내부적으로는 인덱스를 생성하고 문서를 색인하고 문서와 관련된 스키마를 만드는 등 많은 작업을 한다. 좀 더 자세히 알아보자. 

```PUT "localhost:9200/user/_doc/1?pretty"```

PUT은 REST API의 메서드를 나타낸다. 

user는 문서를 색인할 인덱스의 이름이다. 인덱스는 문서를 저장하는 가장 큰 논리적 단위를 의미하며, 같은 성격의 문서들을 하나의 인덱스에 저장하게 된다. 

_doc은 문서 타입의 이름이다. ElasticSearch 5이하에서는 멀티타입을 지원하여 하나의 엔덱스 안에 다양한 타입의 데이터를 저장할 수 있었지만 ElasitcSearch 6이상에서는 인덱스에 하나의 타입만 저장할 수 있게 변경되었다. 

/1은 문서의 ID를 나타낸다. ID는 인덱스 내에서 유일한 값이며, 같은 ID가 입력되면 해당 문서를 수정한다고 인식한다. 

즉 REST API는 user 인덱스의 1번 _doc를 색인해 달라는 요청이다. 색인 과정을 좀 더 논리적으로 확인해보자. 

## 색인 과정 

* 인덱스 존재 여부

색인 요청이 들어오면 인덱스 여부를 확인한다. 인덱스가 존재하지 않는다면 해당 인덱스를 생성한다. 

* 타입 존재 여부

인덱스가 존재하는 것을 확인 후 타입이 존재하는지 확인한다. 마찬가지로 타입이 존재하지 않으면 타입을 생성한다.

* 스키마 존재 여부
스키마가 존재하는지 확인 하고 없다면 스키마를 생성한다. 스키마와 색인 요청된 문서 사이의 충돌이 있는지 확인한다. 이 과정에서 기존 숫자 형태로 정의된 필드에 문자 형태의 값이 들어오면 충돌로 판단하고 에러를 출력한다. 에러가 발생하면 문서는 색인되지 않는다. ( 충돌이 일어난 필드를 제외하고 나머지 필드는 저장하는 옵션이 별도로 존재한다.)

* 문서의 존재 여부
마지막으로 동일한 문서가 존재하는지 파악한다. 만약 동일한 IDrk w






> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyNjQ1NTI3NjIsLTE5MjYwMDAyMjksMT
gyNTI1MjM1OCwxMTcwMzQ5MzE0LDg3NTkxNjI1MSwtMTEwMTMx
Nzg0NSwxOTE0MDEyNzE1XX0=
-->