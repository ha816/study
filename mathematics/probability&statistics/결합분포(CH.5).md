# 중간 정리 
지금까지, 다룬 확률변수, 확률분포 그리고 확률함수의 관계를 정리하면 아래와 같다. 

![ì‹¤í—˜ì˜ í‘œë³¸ê³µê°„ì— ëŒ€í•œ ì´ë¯¸ì§€ ê²€ìƒ‰ê²°ê³¼](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSRMOkmiVh5yEnxQEcUQpzGB2BQgi2E4AdBQx7dUPSvu79Kyaltdg)

1) 확률변수가 발생가능한 결과 표본 공간에서 표본들을 실수 공간의 실수(수치값)으로 대응한다.
2) 대응된 확률 변수들은 다시 확률함수로 대응하는 확률 값을 가진다.

그러면 우리는 왜 세 가지 요소들이 필요하고 어떻게 이용될까? 확률 함수는 확률 변수가 일어날 확률을 나타내는 함수이므로, 우리가 특정 확률 변수의 확률 함수를 알고 있다면, 특정 사건이 일어날 확률을 계산 할 수 있다. 

# 결합 확률밀도함수

지금까지는 확률변수를 하나만 생각했다. 그럼 2개 혹은 그 이상일때에는 어떻게 될까?
확률변수 X, Y와 이에 대응하는 확률밀도함수가 $f(X), f(Y)$가 주어질때, 두 변수를 모두 고려한 확률밀도함수는 **f(X,Y)**로 표현할 수 있다. 그리고 이것이 **결합확률밀도함수(joint probability density function)** 이다. 
  
다음과 같이 정의된 ![](https://t1.daumcdn.net/cfile/tistory/254CC74B58BBB47B34) 를 두 확률변수 X,Y 의  **결합확률밀도함수(joint density function)**라 한다.

  

(1) 이산형: ![](https://t1.daumcdn.net/cfile/tistory/276F413758BBB58F12)

(2) 연속형: ![](https://t1.daumcdn.net/cfile/tistory/23494D3B58BBBFB71D)



**합누적분포함수(joint cumulative distribution function)**는 자연스럽게 다음과 같다.

![](https://t1.daumcdn.net/cfile/tistory/222D243C58BBB78407)

  

그렇다면 여기서 몇가지 정리될 수 있는 사실을 나열해 보면  

## 결합확률질량함수의 성질

* $f(x,y) \ge 0$
* $f(x,y) \ge 0$
* * $f(x,y) \ge 0$



  

(1) 이산형:

![](https://t1.daumcdn.net/cfile/tistory/2749323458BBBFC92B)

  

(2) 연속형:

![](https://t1.daumcdn.net/cfile/tistory/2678D13458BBB8EC30)


https://destrudo.tistory.com/13
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTk5MjE1MzI5LC03Nzk4MDYwODMsNjU3Mz
kxNTgwLC0xNzQ4ODQwOTIyLDE2MDUwMDQ0NjZdfQ==
-->