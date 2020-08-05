# WebFlux

과거 스프링 프레임 워크는

## WebClient 

논블러킹을 지원하는 통신 클라이언트

본질적으로 WebClient는 이전 RestTemplate의 대체품입니다. 그러나 보다 리액티브 방식에 더 잘맞는 함수형 API가 있으며, Flux 또는 Mono와 같은 리액터 프로젝트 타입에 대한 매핑이 내장되어 있습니다. 
```
WebClient.create("uri").get().retrieve().bodyToMono(User.class).map().subscribe()
```

create는 팩터리 메서드이고 기본 URI를 정할 수 있습니다. retreive 메서드는 옵션의 내용을 검색해 조회하거나 다음 처리를 위해 데이터를 준비하는 경우 유용합니다. 
응답 본문을 생성하는 메서드로 bodyToMono를 사용합니다. 이 메서드는 수신 페이로드를 Mono로 변환합니다. 마지막으로 리액터 API를 사용해 응답의 처리 흐름을 구축하고 subscribe 메서드로 원격 호출을 실행합니다.

WebClient는 리액티브 스트림 스펙을 따릅니다. 즉 subscribe 메서드를 호출해서 WebClient가 커넥션을 연결하고 데이터를 원격 서버로 보내기 시작합니다.

대부분의 경우 일반적인 응답 처리는 본문을 처리하는 것이지만, 응답 상태, 헤더 또는 쿠키를 처리하는 경우도 있습니다.

DefaultWebClient는 원격 서버와의 비동기 및 논블로킹 상호 작용을 제공하기 위해 리액터-네티 HttpClient를 사용합니다. 하지만 기본 HTTP 클라이언트를 쉽겨 변경하도록 설계되었습니다. 
WebClient.builer로 사용자가 직접 구현해야 하는 인스턴스를 전달 받을 수 있습니다. 

## 리액티브 웹소켓 API







> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU5MTgxMTc5OSwtMzEwNTM2MTQ1LC04Mj
Y1NDQzMDAsLTE1ODQzNjA5OTUsNDYzNDQ1NTMyLDQ4Njk4NTI5
Miw5NzYxNjgyMjgsMTY2NDU3MTg0MCwxNjM0MDE3NzgsMTEzNj
IzNjE4Niw4NTIxMDMzNywxODYzMTA4Nzk2XX0=
-->