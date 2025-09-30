
@KafkaListener的註解都提供了什麽屬性。

* id：消費者的id，當GroupId沒有被配置的時候，默認id為GroupId  
* containerFactory：
	* 上面提到了@KafkaListener區分單數據還是多數據消費只需要配置一下註解的 containerFactory 屬性就可以了，這裏面配置的是監聽容器工廠，也就是ConcurrentKafkaListenerContainerFactory，配置BeanName 。
	* containerFactory 參數:
		1.  data ： 對於data值的類型其實並沒有限定，根據KafkaTemplate所定義的類型來決定。data為List集合的則是用作批量消費。
		2. ConsumerRecord：具體消費數據類，包含Headers信息、分區信息、時間戳等
		3. Acknowledgment：用作Ack機制的接口
		4. Consumer：消費者類，使用該類我們可以手動提交偏移量、控制消費速率等
* topics：需要監聽的Topic，可監聽多個  
* topicPartitions：可配置更加詳細的監聽信息，必須監聽某個Topic中的指定分區，或者從offset為200的偏移量開始監聽  
* errorHandler：監聽異常處理器，配置BeanName  
* groupId：消費組ID  
* idIsGroup：id是否為GroupId  
* clientIdPrefix：消費者Id前綴  
* beanRef：真實監聽容器的BeanName，需要在 BeanName前加 "__"

@Payload：獲取的是消息的消息體，也就是發送內容  
@Header(KafkaHeaders.RECEIVED_MESSAGE_KEY)：獲取發送消息的key  
@Header(KafkaHeaders.RECEIVED_PARTITION_ID)：獲取當前消息是從哪個分區中監聽到的  
@Header(KafkaHeaders.RECEIVED_TOPIC)：獲取監聽的TopicName  
@Header(KafkaHeaders.RECEIVED_TIMESTAMP)：獲取時間戳


假如配置文件屬性配置了消費組`kafka.consumer.group-id=BASE-DEMO` 正常情況它是該容器中的默認消費組 但是如果設置了 `@KafkaListener(id = "consumer-id7", topics = {"SHI_TOPIC3"})` 那麽當前消費者的消費組就是`consumer-id7` ;
**當然如果妳不想要他作為groupId的話 可以設置屬性`idIsGroup = false`;那麽還是會使用默認的GroupId;**

如何獲取消費者 group.id
在監聽器中調用`KafkaUtils.getConsumerGroupId()`可以獲得當前的groupId; 可以在日誌中打印出來; 可以知道是哪個客戶端消費的;

topics 指定要監聽哪些topic(與topicPattern、topicPartitions 三選一)
可以同時監聽多個 `topics = {"SHI_TOPIC3","SHI_TOPIC4"}`

topicPattern 匹配Topic進行監聽(與topics、topicPartitions 三選一)
可以為監聽器配置明確的主題和分區（以及可選的初始偏移量）

監聽`topic1`的0,1分區；監聽`topic2`的第0分區,並且第1分區從offset為100的開
```java
@KafkaListener(id = "thing2", topicPartitions = {
	@TopicPartition(topic = "topic1", partitions = { "0", "1" }),
	@TopicPartition(topic = "topic2", partitions = "0",
	partitionOffsets = @PartitionOffset(partition = "1", initialOffset = "100"))
})
public void listen(ConsumerRecord<?, ?> record) {
    ...
}
```

errorHandler 異常處理,實現`KafkaListenerErrorHandler`,調用的時候 填寫beanName;例如`errorHandler="kafkaDefaultListenerErrorHandler"`
```java
@Component
public class KafkaDefaultListenerErrorHandler implements KafkaListenerErrorHandler {
    @Override
    public Object handleError(Message<?> message, ListenerExecutionFailedException exception) {
        return null;
    }

    @Override
    public Object handleError(Message<?> message, ListenerExecutionFailedException exception, Consumer<?, ?> consumer) {
     //do someting
        return null;
    }
}
```


containerFactory 監聽器工廠 - 指定生成監聽器的工廠類
```java
/**
 * 監聽器工廠 批量消費
 * @return
 */
@Bean
public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<Integer, String>> batchFactory() {
	ConcurrentKafkaListenerContainerFactory<Integer, String> factory =new ConcurrentKafkaListenerContainerFactory<>();
	factory.setConsumerFactory(kafkaConsumerFactory());
	//設置為批量消費，每個批次數量在Kafka配置參數中設置ConsumerConfig.MAX_POLL_RECORDS_CONFIG
	factory.setBatchListener(true);
	return factory;
}
```
使用`containerFactory = "batchFactory"`

clientIdPrefix 客戶端前綴 - 會覆蓋消費者工廠的`kafka.consumer.client-id`屬性; 最為前綴後面接 `-n` n是數字

concurrency並發數
會覆蓋消費者工廠中的concurrency ,這裏的並發數就是多線程消費; 比如說單機情況下,妳設置了3; 相當於就是啟動了3個客戶端來分配消費分區;分布式情況 總線程數=concurrency*機器數量; 並不是設置越多越好,

```java
/**
     * 監聽器工廠 
     * @return
     */
    @Bean
    public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<Integer, String>> concurrencyFactory() {
        ConcurrentKafkaListenerContainerFactory<Integer, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(kafkaConsumerFactory());
        factory.setConcurrency(6);
        return factory;
    }

    @KafkaListener(id = "consumer-id5",idIsGroup = false,topics = "SHI_TOPIC3", containerFactory = "concurrencyFactory",concurrency = "1)
```
雖然使用的工廠是`concurrencyFactory`(concurrency配置了6); 但是他最終生成的監聽器數量 是1;

properties 配置其他屬性
```java
@KafkaListener(
	id = "consumer-id5",
	idIsGroup = false,
	topics = "SHI_TOPIC3",
	containerFactory = "concurrencyFactory",
	concurrency = "1",
	clientIdPrefix = "myClientId5",groupId = "groupId-test",
	properties = { "enable.auto.commit:false","max.poll.interval.ms:6000"},
	errorHandler="kafkaDefaultListenerErrorHandler"
)
```

KafkaListenerEndpointRegistry
```java
@Autowired
private KafkaListenerEndpointRegistry registry;
   //.... 獲取所有註冊的監聽器
	registry.getAllListenerContainers();
```

當您將Spring Boot與驗證啟動器一起使用時，將LocalValidatorFactoryBean自動配置：如下
```java
@Configuration
@EnableKafka
public class Config implements KafkaListenerConfigurer {

    @Autowired
    private LocalValidatorFactoryBean validator;
    ...

    @Override
    public void configureKafkaListeners(KafkaListenerEndpointRegistrar registrar) {
      registrar.setValidator(this.validator);
    }
}
```

使用
```java
@KafkaListener(
	id="validated",
	topics = "annotated35",
	errorHandler = "validationErrorHandler",
    containerFactory = "kafkaJsonListenerContainerFactory"
)
public void validatedListener(@Payload @Valid ValidatedClass val) {
    ...
}

@Bean
public KafkaListenerErrorHandler validationErrorHandler() {
    return (m, e) -> {
        ...
    };
}
```


## 什麽情況下設置concurrency,以及設置多少

這個得看我們給Topic設置的分區數量; 總的來說就是  **機器數量 * concurrency <= 分區數**
例如分區=3; 而且同時有3臺機器 ,那麽concurrency=1就行了; 設置多了就會浪費資源;、
例如分區=9; 只有3臺機器;那麽可以concurrency=3 ; 每臺機器3個消費者連接3個分區; 那麽妳可能會問我們concurrency=1不也可以嗎; 反正都是一臺機器消費3個分區; 話是沒有錯; 但是他們的差別在 一個線程消費3個分區和 3個線程消費3個分區 , **單線程和多線程**妳選哪個

## NewTopic
* auto.create.topics.enable=true
* 下面的config会建立新的topics，如果已经存在，这个bean会被忽略。
* Spring Boot 會自動註冊 KafkaAdmin，KafkaAdmin 會自動將我們設定的 NewTopic Bean 新增到 Kafka Server 的 Topic。
	```java
	//如果不存topics，通过NewTopic新建
	@Bean
	public NewTopic defaultTopic() {
		return TopicBuilder.name(JSON_TOPIC).build();
	}
	```
	```java
	@Bean
	public NewTopic logCenter() {
		return new NewTopic("logCenter", 2, (short) 2);
	}
	```
	```java
	/**
	 * 项目启动时，自动创建topic，指定分区和副本数量
	 * @return Topic
	 */
	@Bean
	public NewTopic topic(){
		return new NewTopic(topic, patitions, replications);
	}
	```

--- 

在我們之前的代碼中，我們是通過註入 `NewTopic` 類型的對象來創建 Kafka 的 topic 的。當我們的項目需要創建的 topic 逐漸變多的話，通過這種方式創建就不是那麽友好了，我覺得主要帶來的問題有兩個：

1. Topic 信息不清晰；
2. 代碼量變的龐大；

```java
    /**
     * 通過註入一個 NewTopic 類型的 Bean 來創建 topic，如果 topic 已存在，則會忽略。
     */
    @Bean
    public NewTopic myTopic() {
        return new NewTopic(myTopic, 2, (short) 1);
    }

    @Bean
    public NewTopic myTopic2() {
        return new NewTopic(myTopic2, 1, (short) 1);
    }
```

今天說一下我對於這個問題的解決辦法：

在 `application.xml` 配置文件中配置 Kafka 連接信息以及我們項目中用到的 topic。

```yaml
server:
  port: 9090
spring:
  kafka:
    bootstrap-servers: localhost:9092
kafka:
  topics:
    - name: topic1
      num-partitions: 3
      replication-factor: 1
    - name: topic2
      num-partitions: 1
      replication-factor: 1
    - name: topic3
      num-partitions: 2
      replication-factor: 1
```

`TopicConfigurations` 類專門用來讀取我們的 topic 配置信息：

```java

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;
import org.apache.kafka.clients.admin.NewTopic;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

import java.util.List;

@Configuration
@ConfigurationProperties(prefix = "kafka")
@Setter
@Getter
@ToString
class TopicConfigurations {
    private List<Topic> topics;

    @Setter
    @Getter
    @ToString
    static class Topic {
        String name;
        Integer numPartitions = 3;
        Short replicationFactor = 1;

        NewTopic toNewTopic() {
            return new NewTopic(this.name, this.numPartitions, this.replicationFactor);
        }

    }
}

```

在 `TopicAdministrator` 類中我們手動將 topic 對象註冊到容器中。

```java

import org.apache.kafka.clients.admin.NewTopic;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.context.support.GenericWebApplicationContext;

import javax.annotation.PostConstruct;
import java.util.List;

/**
 * @author shuang.kou
 */
@Configuration
public class TopicAdministrator {
    private final TopicConfigurations configurations;
    private final GenericWebApplicationContext context;

    public TopicAdministrator(TopicConfigurations configurations, GenericWebApplicationContext genericContext) {
        this.configurations = configurations;
        this.context = genericContext;
    }

    @PostConstruct
    public void init() {
        initializeBeans(configurations.getTopics());
    }

    private void initializeBeans(List<TopicConfigurations.Topic> topics) {
        topics.forEach(t -> context.registerBean(t.name, NewTopic.class, t::toNewTopic));
    }


}

```

這樣的話，當我們運行項目之後，就會自動創建 3 個名為：topic1、topic2 和 topic3 的主題了。

---

** ProducerInterceptor、ConsumerInterceptor、RecordInterceptor` (Spring) **