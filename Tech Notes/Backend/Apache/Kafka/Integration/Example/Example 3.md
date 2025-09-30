


```java
 @KafkaListener(id = "consumer-id",topics = "SHI_TOPIC1",concurrency = "${listen.concurrency:3}", clientIdPrefix = "myClientId")
```
属性`concurrency`将会从容器中获取`listen.concurrency`的值,如果不存在就默认用3

id 监听器的id 填写:
```java
2020-11-19 14:24:15 c.d.b.k.KafkaListeners 120 [INFO] 线程:Thread[**`consumer-id5-1-C-1`**,5,main]-groupId:BASE-DEMO consumer-id5 消费
```
没有填写ID:
```java
2020-11-19 10:41:26 c.d.b.k.KafkaListeners 137 [INFO] 线程:Thread[**`org.springframework.kafka.KafkaListenerEndpointContainer#0-0-C-1`**,5,main] consumer-id7
```
在相同容器中的监听器ID不能重复
```text
Caused by: java.lang.IllegalStateException: Another endpoint is already registered with id
```