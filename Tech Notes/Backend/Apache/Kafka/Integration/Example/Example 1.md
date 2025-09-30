
[Event-Driven Architectures with Kafka and Java Spring-Boot](https://thepracticaldeveloper.com/spring-boot-kafka-config/)


## Producer

```java
public class TimestampEvent {  
	private ZonedDateTime timestamp;  
	  
	public TimestampEvent(ZonedDateTime timestamp) {  
	this.timestamp = timestamp;  
	}  
	  
	public ZonedDateTime getTimestamp() {  
	return timestamp;  
	}  
	  
	public void setTimestamp(ZonedDateTime timestamp) {  
	this.timestamp = timestamp;  
	}  
}
```

```java
public class ProducerConfiguration {

    @Bean
    public ProducerFactory < String, TimestampEvent > producerFactory() {
        var props = new HashMap < String,
            Object > ();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "tutorialGroup");
        return new DefaultKafkaProducerFactory < > (props);
    }

    @Bean
    public KafkaTemplate < String, TimestampEvent > kafkaTemplate() {
        return new KafkaTemplate < > (producerFactory());
    }

    @Bean
    public NewTopic timestampTopic() {
        return TopicBuilder.name("timestamp")
            .build();
    }
}
```

In `reportCurrentTime()` the timestamp is being sent to Kafka every 5 seconds, which is implemented via the `@Scheduled`-annotation.
```java
@Component
public class KafkaProducer {
    private static final Logger log = LoggerFactory.getLogger(KafkaProducer.class);

    @Autowired
    private KafkaTemplate < String, TimestampEvent > kafkaTemplate;

    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
        var event = new TimestampEvent(ZonedDateTime.now());
        kafkaTemplate.send("timestamp", event);
        log.info("Sent: {}", event.getTimestamp().toString());
    }
}
```


## Consumer

```java
package net.wissmueller.kafkatutorial.consumer;

import java.time.ZonedDateTime;

public class TimestampEvent {
  private ZonedDateTime timestamp;

  public TimestampEvent() {}

  public ZonedDateTime getTimestamp() {
    return timestamp;
  }

  public void setTimestamp(ZonedDateTime timestamp) {
    this.timestamp = timestamp;
  }
}
```


As with the producer, the entry-point is the application class in `ConsumerApplication.java`.
```java
package net.wissmueller.kafkatutorial.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ConsumerApplication {

  public static void main(String[] args) {
    SpringApplication.run(ConsumerApplication.class, args);
  }

}
```


```java
  
@Configuration
public class ConsumerConfiguration {

  @Bean
  public ConsumerFactory<String, TimestampEvent> consumerFactory() {
    var timestampEventDeserializer = new JsonDeserializer<TimestampEvent>(TimestampEvent.class);
    timestampEventDeserializer.setRemoveTypeHeaders(false);
    timestampEventDeserializer.addTrustedPackages("*");
    timestampEventDeserializer.setUseTypeMapperForKey(true);

    var props = new HashMap<String, Object>();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
    props.put(ConsumerConfig.GROUP_ID_CONFIG, "tutorialGroup");

    return new DefaultKafkaConsumerFactory<>(props, new StringDeserializer(), timestampEventDeserializer);
  }

  @Bean
  public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<String, TimestampEvent>> kafkaListenerContainerFactory() {
    var factory = new ConcurrentKafkaListenerContainerFactory<String, TimestampEvent>();
    factory.setConsumerFactory(consumerFactory());
    return factory;
  }

  @Bean
  public NewTopic timestampTopic() {
    return TopicBuilder.name("timestamp")
                       .build();
  }

}
```

```java
@Component
public class KafkaConsumer {
  private static final Logger log = LoggerFactory.getLogger(KafkaConsumer.class);

// containerFactory = beanName of the ConcurrentKafkaListenerContainerFactory implementation
  @KafkaListener(topics = "timestamp", containerFactory = "kafkaListenerContainerFactory")
  void listener(TimestampEvent event) {
    log.info("Received: {}", event.getTimestamp().toString());
  }
}

```
