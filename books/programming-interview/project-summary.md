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

상품별 배송도착 소요일의 평균을  고객에게 제공.

<![if !supportLists]>n <![endif]>상품 판매자에게 더 빠른 배송 서비스를 제공하도록 유도.

<![if !supportLists]>**** <![endif]>**프로젝트 개발**

<![if !supportLists]>**n** <![endif]>**Spring Batch** **개발**

<![if !supportLists]>**-** <![endif]>상품 별로 최근 1주일치  배송건의 배송 소요시간의  평균을 계산.

<![if !supportLists]>- <![endif]>**평균 배송소요일 계산대상 상품 수는 약**100,000 건, 상품 별로 평균 하루 10건의 배송이 존재.

<![if !supportLists]>- <![endif]>프로토 배치는 1시간 정도 시간이 소요. 아래 전략을 통해 소요시간을 15분 내외로 단축

<![if !supportLists]>- <![endif]>소요 시간을 줄이기 위해서 평균 계산일 계산 알고리즘 개선. 이전에 계산된 배송소요일이 있으면, 가장 오래된 날짜의 배송소요일을 제거하고 추가할 날짜의 배송소요일을 추가해서 불필요한 반복 계산을 회피

<![if !supportLists]>- <![endif]>계산 처리 단위를 1000건씩 묶어 처리하여 네트워크 비용 감소.

<![if !supportLists]>- <![endif]>Spring Batch의 Partition을 이용한 멀티쓰레드 처리.

<![if !supportLists]>n <![endif]>RDB 구축

<![if !supportLists]>- <![endif]>평균 배송 소요일은 하루에 한번 재계산 되기 때문에 주문시점에 노출되던 평균배송소요일을 스냅샷 으로 기록하는 테이블이 필요. 해당 테이블에 적재를 위한 REST API 제공.

<![if !supportLists]>n <![endif]>CouchBase 적재

<![if !supportLists]>- <![endif]>노출 서비스 사용처에서 필요 정보를 RDB로 제공하면 요청 수가 많아 DB성능상 이슈 발생.

<![if !supportLists]>- <![endif]>별도의 CouchBase계산된 평균배송소요일 추가 적재.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU3OTA4MDIyOCwtMTIxOTQ0NTUxNyw2Mj
k5ODk2OTQsLTE0MTc4NzUzMjksLTEwNTI0NDU1ODQsMTU5ODkw
NTM0MSwtMTMyOTc2MjIzMywtODcyMDYyMDY4LDYyNjIyMTgwMC
wxNjM1MTcwMiwtNTUzNjcwMzg2XX0=
-->