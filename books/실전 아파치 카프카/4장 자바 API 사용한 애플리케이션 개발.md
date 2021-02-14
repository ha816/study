# Overview

자바 API를 이용하여 애플리케이션을 만들어보자. 카프카는 자바 API를 제공하고 이로써 카프카와 메시지를 송수신하는 애플리케이션을 만들 수 있다. 카프카의 자바 API에는 프로듀서용, 컨슈머용 API가 존재한다. 각 애플리케이션을 실제 작성하여 송신에서부터 수신까지 일련의 흐름을 알아보자. 

# 애플리케이션 개발 환경 준비

JDK, Gradle, Maven 등 애플리케이션이 필요하다. 

# 프로듀서 애플리케이션

카프카의 자바 API로 메시지를 송신하기 위해서는 KafkaProducer 객체를 이용한다. 예제 코드에선 KafkaProducer에 필요한 설정을 하고 객체를 생성한다. 

```
Properties conf = new Properties();
conf.setProperty("bootstrap.servers", "kafka-broker01:9092, kafka-broker02:9092, kafka-broker3:9092");

conf.setProperty("key.serializer", "org.apache.kafka.common.serialization.IntegerSerializer");

conf.setProperty("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

Producer<Integer,>


```


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNTExNjU2ODQsNDQ4OTA0MzNdfQ==
-->