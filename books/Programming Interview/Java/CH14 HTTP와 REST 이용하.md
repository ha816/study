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
...
<!DOCTYPE html>
...
CONTENT
</html>
```

첫번째 행 HTTP/1.0 200 OK은 1.0버전의 HTTP 프로토콜을 잉요하여 클라이언트에 응답을 하고 성공적인 응답인 200 OK 결과를 보낸다.(이 서버는 1.1버전의 요청을 받아들인 후 1.0응답으로 응답한다)
두번째 행 부터는 응답에 대한 



 

## HTTP 메서드



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbODc0MTcxMTcyLC0xODMyOTg1Mzc4LDczMD
k5ODExNl19
-->