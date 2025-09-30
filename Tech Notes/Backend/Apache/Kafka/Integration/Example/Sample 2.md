https://www.tpisoftware.com/tpu/articleDetails/2518

**Event**：事件，指的是當有什麼事情發生時所保留下的紀錄或訊息，當在對 Kafka 做讀寫時，就是以事件的形式來做這些事情。一個事件包含鍵(key)、值(value)、時間戳章(timestamp)，或是可選的元資料頭( metadata headers )

**Producers** ：生產者，指的是向 Kafka 發佈事件的應用程式。

**Consumers** ：消費者，指的是向 Kafka 訂閱事件的應用程式。

**Topic**：主題，事件被組織化、持久的儲存在主題中。一個主題可以由零個到多個生產者和消費者去發佈或訂閱它。主題中的事件是隨時可以被讀取的，事件被消費後不被刪除。使用者可以透過配置設定每個主題中的事件可以保留多長的時間，而 Kafka 的性能相對於數據的大小是恆定的，因此長時間的儲存數據是完全沒問題的。

**Partition**：分區，主題分佈在位於不同 Kafka 代理的多個存儲空間中。數據的分佈式放置對於可擴展性非常重要，因為它讓客戶端應用程式同時向多個代理讀取和寫入數據。當一個新的事件發佈到主題中，它實際上被附加到某個主題的分區之一裡，Kafka 確保訂閱此分區的消費者在讀取的順序與事件寫入的順序是完全相同的。

**Offset**：偏移量，在分區中的每個訊息都有的、一個遞增的 id。

**Broker & Cluster**：broker 指運行 Kafka 的機器，而 cluster 則是指多個 broker 組成的叢集。

**Replicas**：備份，為了使數據有容錯性與高可用，每個主題都能夠備份，甚至可以跨越地理區域或數據中心備份。

**Leader & ISR**：Leader broker 主要負責對於特定分區的讀寫，而 in-sync-replicas (ISR) 表示同步中的 broker；若 Leader broker 無法運作，zookeeper 會指派其中一個 ISR 來替代作為 Leader broker。

```pom
	<dependency>
		<groupId>org.springframework.kafka</groupId>
		<artifactId>spring-kafka</artifactId>
	</dependency>
```


# Producer 

```java
@Configuration
public class KafkaConfig {

    public static final String JSON_TOPIC = "json";
    public static final String DEFAULT_SERVER = "127.0.0.1:9092";

    @Bean
    public ProducerFactory<String, UserVo> producerFactory() {
        Map<String, Object> config = new HashMap<>();

        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, DEFAULT_SERVER);
        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);

        return new DefaultKafkaProducerFactory<>(config);
    }

    @Bean
    public KafkaTemplate<String, UserVo> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }

    @Bean
    public NewTopic defaultTopic() {
        return TopicBuilder.name(JSON_TOPIC).build();
    }
}
```

● producerFactory：註冊一個 Kafka Producer 實體用來產生訊息，這裡設定了Kafka Server 的位址在本機端的 9092 port 、Key 和 Value 的序列化方式。

● kafkaTemplate：註冊一個 KafkaTemplate，他包裝了 Producer 實體並且提供我們一些便利的方法來發送訊息到 Topic。

● defaultTopic：Spring Boot 會自動註冊 KafkaAdmin，KafkaAdmin 會自動將我們設定的 NewTopic Bean 新增到 Kafka Server 的 Topic。

```java
@RestController
@RequestMapping("kafka")
public class ProducerCtrl {

    private static final Logger LOGGER = LoggerFactory.getLogger(ProducerCtrl.class);

    @Autowired
    private KafkaTemplate<String, UserVo> kafkaTemplate;

    @GetMapping("/publish/{dpt}/{id}")
    public String post(@PathVariable("dpt") final String dpt, @PathVariable("id") final String id) {

        UserVo userVo = new UserVo(id, dpt);

        kafkaTemplate.send(KafkaConfig.JSON_TOPIC, userVo);


        return "Published done";
    }
}
```

```java
public class UserVo {

    private String id;
    private String dpt;

    public UserVo(String id, String dpt) {
        super();
        this.id = id;
        this.dpt = dpt;
    }

    public String getDpt() {
        return dpt;
    }

    public void setDpt(String dpt) {
        this.dpt = dpt;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    @Override
    public String toString() {
        return "UserVo [id=" + id + ", dpt=" + dpt + "]";
    }

}
```

# Consumer Project

```java
@EnableKafka
@Configuration
public class KafkaConfig {

    public static final String TEST_TOPIC = "test";
    public static final String JSON_TOPIC = "json";
    public static final String DEFAULT_SERVER = "127.0.0.1:9092";
    public static final String GROUP_1 = "group_1";
    public static final String GROUP_2 = "group_2";
    private static final Logger LOGGER = LoggerFactory.getLogger(KafkaConfig.class);


    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> config = new HashMap<>();

        config.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, DEFAULT_SERVER);
        config.put(ConsumerConfig.GROUP_ID_CONFIG, GROUP_1);
        config.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        config.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);

        return new DefaultKafkaConsumerFactory<>(config);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
                new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }

    @Bean
    public ConsumerFactory<String, UserVo> userConsumerFactory() {
        Map<String, Object> config = new HashMap<>();

        config.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, DEFAULT_SERVER);
        config.put(ConsumerConfig.GROUP_ID_CONFIG, GROUP_2);
        config.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        config.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(config);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, UserVo> userKafkaListenerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, UserVo> factory =
                new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(userConsumerFactory());
        return factory;
    }

}
```

● consumerFactory()、userConsumerFactory()：註冊一個 Kafka Consumer 實體用來訂閱訊息，這裡設定了Kafka Server 的位址在本機端的 9092 port 、Key 和 Value 的反序列化方式。

● kafkaListenerContainerFactory()、userKafkaListenerFactory()：註冊一個 Container 給有註解 @KafkaListener 的方法。

● @EnableKafka：必須在設定檔上加此註解，用來啟用偵測 @KafkaListener 的方法


```java
package com.example.demo;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;

@Service
public class KafkaConsumer {

    private static final Logger LOGGER = LoggerFactory.getLogger(KafkaConsumer.class);

    @KafkaListener(topics = KafkaConfig.TEST_TOPIC, groupId = KafkaConfig.GROUP_1)
    public void consume(String message) {
        LOGGER.info("Consumed message: {} ", message);
    }


    @KafkaListener(topics = KafkaConfig.JSON_TOPIC, groupId = KafkaConfig.GROUP_2,
            containerFactory = "userKafkaListenerFactory")
    public void consumeJson(UserVo user) throws InterruptedException {
        LOGGER.info("Consumed JSON Message: {} ", user);
    }
}
```

```java
public class UserVo {

    private String id;
    private String dpt;

    public UserVo() {}

    public String getDpt() {
        return dpt;
    }

    public void setDpt(String dpt) {
        this.dpt = dpt;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    @Override
    public String toString() {
        return "UserVo [id=" + id + ", dpt=" + dpt + "]";
    }

}
```

當 Producer 序列化型態與 Consumer 在反序列化型態無法匹配時，毒藥訊息的情境就產生了。 **使用 ErrorHandlingDeserializer**

另外這邊多配置 org.springframework.kafka.listener.LoggingErrorHandler 取代 container 預設的 SeekToCurrentErrorHandler 用來印錯誤訊息。
```java
@Bean
public ConcurrentKafkaListenerContainerFactory<String, UserVo> userKafkaListenerFactory() {
    ConcurrentKafkaListenerContainerFactory<String, UserVo> factory =
            new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(userConsumerFactory());
    // add ErrorHandler
    factory.setErrorHandler(errorHandler()); 
    return factory;
}

@Bean
public LoggingErrorHandler errorHandler() {
    return new LoggingErrorHandler();
}
```


如果希望在 Producer 發佈訊息時多做一些處理，比如說寫進 Database 或是印自訂的 LOG 等等，可以接 send 方法回傳的 ListenableFuture<SendResult>，利用非同步的方式做後續處理。改寫 Producer 發佈訊息的方法為下：
```java
@RestController
@RequestMapping("kafka")
public class ProducerCtrl {

    private static final Logger LOGGER = LoggerFactory.getLogger(ProducerCtrl.class);

    @Autowired
    private KafkaTemplate<String, UserVo> kafkaTemplate;

    @GetMapping("/publish/{dpt}/{id}")
    public String post(@PathVariable("dpt") final String dpt, @PathVariable("id") final String id) {

        UserVo userVo = new UserVo(id, dpt);

        ListenableFuture<SendResult<String, UserVo>> future =
                kafkaTemplate.send(KafkaConfig.JSON_TOPIC, userVo);

        future.addCallback(new KafkaSendCallback<String, UserVo>() {

            @Override
            public void onSuccess(SendResult<String, UserVo> result) {
                LOGGER.info("success send message:{} with offset:{} ", userVo,
                        result.getRecordMetadata().offset());
            }

            @Override
            public void onFailure(KafkaProducerException ex) {
                LOGGER.error("fail send message! Do somthing....");
            }
        });

        return "Published done";
    }
}
```

Spring-Kafka 提供 AbstractKafkaListenerContainerFactory 來配置  spring-retry template，在 retry template 中可以設定要重試幾次以及每次重試間隔的時間多久。

```java
@Bean
public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
    ConcurrentKafkaListenerContainerFactory<String, String> factory =
            new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(consumerFactory());    
    factory.setRetryTemplate(kafkaRetry());
    factory.setRecoveryCallback(retryContext -> {
        ConsumerRecord<String, String> consumerRecord =
                (ConsumerRecord) retryContext.getAttribute("record");
        LOGGER.info("Recovery is called for message: {} ", consumerRecord.value());
        return Optional.empty();
    });
    return factory;
}

@Bean
publci RetryTemplate kafkaRetry() {
    RetryTemplate retryTemplate = new RetryTemplate();

    FixedBackOffPolicy fixedBackOffPolicy = new FixedBackOffPolicy();
    fixedBackOffPolicy.setBackOffPeriod(5 * 1000l);
    retryTemplate.setBackOffPolicy(fixedBackOffPolicy);

    SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy();
    retryPolicy.setMaxAttempts(3);
    retryTemplate.setRetryPolicy(retryPolicy);
    return retryTemplate;
}
```

這裡 retry template 配置了重試 3 次、每次間隔 5 秒。如果下游系統在 retry 的期間恢復運作，那訊息就可以繼續傳遞。
另外還有設置了 RecoveryCallback，當 retry 都結束了就會觸發，以便做後續處理。