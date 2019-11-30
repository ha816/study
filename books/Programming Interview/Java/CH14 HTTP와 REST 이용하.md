# HTTP

>HTTP란?

HTTP(Hyper Text Transfer Protocol)는 인터넷을 통해 데이터를 주고 받는데 하나의 프로토콜이다. HTTP는 월드와이드웹(WWW)을 통해 소통하는데 주로 사용된다. 

탐캣과 같은 HTTP 서버는 요청을 체계적으로 처리하여 적절한 응답을 한다. 

## HTTP 요청

HTTP 요청은 아래와 같다.
```
GET / HTTP/1.1
```
`GET / HTTP/1.1` 의 의미는 순서대로 Get메서드를 이용해서 '/' 페이지를 얻고 '1.1'버전의 HTTP을 사용한다라는 의미이다. 

HTTP 응답은 아래와 같다. 응답의 경우 크게 헤더와 컨텐츠로 나누어진다. 
```
HTTP/1.0 200 OK
Server: Apache
X-Content-Type: nosniff
Content-Length: 6071
Content-Type: text/html; charset=utf-8
...
<!DOCTYPE html>
...
CONTENT
</html>
```

첫번째 행 HTTP/1.0 200 OK은 1.0버전의 HTTP 프로토콜을 잉요하여 클라이언트에 응답을 하고 성공적인 응답인 200 OK 결과를 보낸다.(이 서버는 1.1버전의 요청을 받아들인 후 1.0응답으로 응답한다)
두번째 행 부터는 응답에 대한 관련 메타 데이터가 있는 응답헤더들이다. 
컨텐츠으로는 HTML 뿐만 아니라 순수한 텍스트나 바이너리 데이터도 된다. 일반적인 컨텐츠 형식은 Content-type 헤더로 들어간다. 

## HTTP 메서드

HTTP메서드는 동사와 같은 역할을 한다. 웹 서버에 요청한 자원을 이용해서 무엇을 할지 알려주기 때문이다.  서버가 일반적으로 구현하는 메서드 중 HEAD, GET, POST는 필수적인 메서드이다. 

HEAD
: GET 요청과 비슷하지만 응답 코드와 헤더만 반환한다. 서버의 다운 여부 점검(Health Check)이나 웹 서버 정보(버전 등)등을 얻기 위해 사용될 수 있다.  

POST와 PUT의 차이점이 혼란스러울수 있다. POST는 자원을 새로 생성하고 PUT은 기존 자원이 없으면 새로 생성하고 있으면 덮어쓴다. 다른 말로는 PUT은 **멱등성**(idempotent)은 따르고 POST는 따르지 않는다. 

추가적으로 HTTP 명세에서는 OPTIONS라는 메서드를 구현하라고 강조하는데, 이 메서드는 클라이언트 입장에서 어떤 메서드를 사용할 수 있는지 알려주는 메서드이다. 

## HTTP 응답 코드

응답 코드의 첫번째 숫자에 따라 응답의 분류가 있는데, 2XX로 시작하는 코드는 요청과 응답이 성공적이라는 것을 알려준다. 

* 201 Created: 주로 PUT요청이 성공했고 자원이 생성되었다는걸 말한다.
* 204 Content: 요청은 성공했지만, 서버는 추가 컨텐츠 정보를 제공하지 않는다. 보통 PUT, POST, DELETE에 성공했을때 사용한다.

4XX는 클라이언트의 에러, 즉 요청이 잘못되었다는것을 뜻한다. 
* 400 Bad Request: 클라이언트가 잘못된 요청을 보냈다. 보통 필수적인 파라미터를 뺀경우 많이 발생한다.
* 403 Forbidden: 로그인했지만 주어진 자원을 요청할 권한이 없음
* 404 Not Found: 요청한 자원이 존재하지 않음
* 405 Method Not Allowed: 지원하지 않는 HTTP 메서드를 이용

5XX는 서버측에 문제가 있다는 응답이다. 대개는 서버에서 조취를 취해줘야 예외상황이 해결된다. 

* 500 Internal Server Error: 일반적인 메세지로, 보통 전체 서버를 이용할수 없는 상황이 아니라 일부의 자원만 이용할 수 없을때 발생한다.
* 503 Service Unavailable: 현재 서버를 이용할 수 없다. 일시적인 중단 상태이다. 

## HTTP 클라이언트




  


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjQwMjI4OTAyLC0xODMyOTg1Mzc4LDczMD
k5ODExNl19
-->