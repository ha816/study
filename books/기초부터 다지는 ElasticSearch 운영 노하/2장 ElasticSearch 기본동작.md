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




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU5Mjg5MDcyNCw4NzU5MTYyNTEsLTExMD
EzMTc4NDUsMTkxNDAxMjcxNV19
-->