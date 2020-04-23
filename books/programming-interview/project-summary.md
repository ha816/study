# TMON

## 배송상품 선물하기 서비스

기존 티몬 배송 서비스에서 상품 수령자는 반드시 구매자였다. 본 프로젝트는  구매자가 아닌 고객도 상품을 수령 받을 수 있게 구조를 고치는 프로젝트였다. 이로써 티몬의 배송 상품을 회원 고객(구매자)뿐만 아니라 비회원 고객도 가능하게 되었다. 
```
주문 시점(배송지 입력)-> 주문 완료 -> 배송시작
주문 시점(배송지 미입력, 수령자정보 입력)-> 주문 완료 -> 수령자 알림 및 배송지 입력 -> 배송시작 
```

### 주요 역할

* 요구사항에 맞는 DB 테이블 설계
	* gift 테이블( 수령자 번호, 수령자 별칭, 배송지 입력시기, 선물 메시지 등)
* 신규 프로세스 및 API 개발
	* 주문 완료 이후 배송지 입력 API 제공 
	* 선물 주문 구매내역만 보여주는 선물함 API지원
	* 수령자 알림 서비스(비즈톡, LMS)

## 도서산간 배송시스템 구축

일반적으로 도서산간 지역에 배송시 추가 배송비가 발생한다. 추가 발생 배송비를 고객이 후불로 지불했는데 선결제가 가능하도록 구조를 고치고 도서산간 지역을 관리하는 시스템을 구축했다. 

### 주요 역할

* 우편번호 기반 도서산간 지역 테이블 구축
* 어드민 개발
	* 도서산간 지역 조회 기능
* 배송 정책에 일치하는 경우, 배송비 선결제가 가능
* 고객 환불시 도서산간 추가 배송비 계산 로직 수정

## 평균 배송소요일 계산 및 적재

티몬의 상품 페이지에 평균적으로 평일만에 상품이 도착했는지를 보여주는 정보가 바로 평균 배송소요일이다. 평균치를 계산하기 위해 Spring Batch와 Couchbase를 사용했다. 
상품 별로 최근 1주일치  배송건이 대상으로 계산대상 상품 수는 약 100,000 건, 상품 별로 평균 하루 10건이 존재했다.

### 주요 역할

* Spring Batch를 이용한 개발
	* 성능 향상을 위해 알고리즘 개선, Partition을 이용한 멀티쓰레드, 벌크 처리 등
* 주문 시점에 노출되던 평균 배송소요일을 저장하기 위한 DB 테이블 구축 및 Rest API 제공
* CouchBase 적재
	* 딜 페이지에 노출시 RDB 데이터 제공시 DB 부하가 발생할 소지가 있어 Couchbase에 평균 배송소요일을 적재


# 연구 논문

## 다형질  데이터를  이용한  영화  추천  시스템

Matrix factorization이란 주어진 매트릭스를 분해하여 요소를 구하고.





<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQwNjg2NTM4OSwtMTYyMjY1NTIxNSwzNz
M2NzQwNTAsLTc4NDM0ODYxNiwtNDA5NTE5NDE1LDIwMTQ1Mjg5
MDgsLTQwOTUxOTQxNSwtMTIxOTQ0NTUxNyw2Mjk5ODk2OTQsLT
E0MTc4NzUzMjksLTEwNTI0NDU1ODQsMTU5ODkwNTM0MSwtMTMy
OTc2MjIzMywtODcyMDYyMDY4LDYyNjIyMTgwMCwxNjM1MTcwMi
wtNTUzNjcwMzg2XX0=
-->