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

Producer<Integer, String> producer = new KafkaProducer<>(conf);
```

위 코드는 동작하는데 필요한 최소한의 설정만 세팅하고 있다. 
bootstraps.servers는 KafkaProducer가 메시지를 보낼 브로커의 호스트명과 포트번호를 저장하고 있다. 

카프카에서는 모든 메시지가 직렬화된 상태로 전송된다. 이때 key.serializer, value.serializer가 사용된다. 

카프카에서는 기본적인 시리얼라이저가 준비되어 있다. 또 커스터마이징 시리얼라이저를 직접 만들어 사용할 수도 있다. 

Producer를 보면 Generic 타입으로 Key와 Value를 받는다. 위 코드에서는 Key로 Integer, Value로 문자형을 받고 있다. 여기서 지정한 형은 시리얼 라이저와 대응해야 정상 동작한다. 

## 메시지 송신하기 

```
// 송신메시지 생성
ProducerRecord<Integer, String> record = new ProducerRecord<>(topicName, key, value);
```

KafkaProducer를 이용하여 메시지를 보낼때는 송신 메시지를 ProducerRecord라는 객체에 저장한다. 이때 Key,Value외에도 topicName도 함께 등록한다. 

```
producer.send(record, new CallBack(){

	@Override
	public void onCompletion(RecordMetadata metadata, Exception e) {
		if(metadata != null) {
			metadata.partition();
			metadata.offset();
		} else { 
			// Failed 
			e.getMessage();
		}
	}
});
```

위 Callback 클래스를 구현하여 KafkaProducer에 전달하고 있다. 이 Callback 클래스에서 구현하고 있는 onCompletion 메서드에서는 송신을 완료했을때 실행되어야 할 처리를 하고 있다. 

KafkaProducer의 송신 처리는 비동기적으로 이루어지기 때문에 send 메서드를 호출할때 바로 처리가 되지 않는다. send 메서드의 처리는 송신 큐에 메시지를 넣을뿐이다. 송신 큐에 넣은 메시지는 사용자 애플리케이션과는 다른 별도의 쓰레드에서 순차적으로 송신된다. 

메시지가 송신된 이후 카프카 클러스터에서 Ack가 반환된다. Callback 클래스는 그 Ack를 수신했을때 처리된다. 

Callback 클래스의 메서드는 메시지 송신에 성공했을때와 실패했을때 동일한 내용이 호출된다. 메시지 송신에 성공하면 메서드 인수인 RecordMetadata가 null이 아닌 객체이고 Exception은 null이 된다.

# 컨슈머 애플리케이션 개발

```
Properties conf = new Properties();
conf.setProperty("bootstrap.servers", "kafka-broker01:9092, kafka-broker02:9092, kafka-broker3:9092");
conf.setProperty("key.serializer", "org.apache.kafka.common.serialization.IntegerSerializer");
conf.setProperty("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

Consumer<Integer, String> consumer = new KafkaConsumer<>(conf);
consumer.subscribe(Collections.singletonList(topicName));

for(int count = 0; count < 300; count++) {
	

}

```



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1OTIzODQ3NzUsLTc1OTYxNjIxMyw3Mj
E1ODY1MzksLTEyMzM2OTY3NjcsLTE1MjQ2NzM5LC0xMjE4NDc1
NTUsNDQ4OTA0MzNdfQ==
-->