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

POST와 PUT의 차이점이 혼란스러울수 있다. POST는 자원을 새로 생성하고 PUT은 기존 자원이 없으면 새로 생성하고 있으면 덮어쓴다. PUT은 멱
  


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY5NTUwNzY5OCwtMTgzMjk4NTM3OCw3Mz
A5OTgxMTZdfQ==
-->