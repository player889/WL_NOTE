
## 消息监听容器

### 1、KafkaMessageListenerContainer

由spring提供用于监听以及拉取消息，并将这些消息按指定格式转换后交给由@KafkaListener注解的方法处理，相当于一个消费者；

-   可以发现其入口方法为doStart(), 往上追溯到实现了SmartLifecycle接口，很明显，由spring管理其start和stop操作；
-   ListenerConsumer, 内部真正拉取消息消费的是这个结构，其 实现了Runable接口，简言之，它就是一个后台线程轮训拉取并处理消息
-   在doStart方法中会创建ListenerConsumer并交给线程池处理
-   以上步骤就开启了消息监听过程

### 2、ConcurrentMessageListenerContainer
并发消息监听，相当于创建消费者；其底层逻辑仍然是通过KafkaMessageListenerContainer实现处理；从实现上看就是在KafkaMessageListenerContainer上做了层包装，有多少的concurrency就创建多个KafkaMessageListenerContainer，也就是concurrency个消费者
ConcurrentMessageListenerContainer#doStart

上面已经介绍了`KafkaMessageListenerContainer`  的作用是拉取并处理消息，但还缺少关键的一步，即 如何将我们的业务逻辑与KafkaMessageListenerContainer的处理逻辑联系起来？

那么这个桥梁就是@KafkaListener注解
-   KafkaListenerAnnotationBeanPostProcessor, 从后缀BeanPostProcessor就可以知道这是Spring IOC初始化bean相关的操作，当然这里也是；此类会扫描带@KafkaListener注解的类或者方法，通过 KafkaListenerContainerFactory工厂创建对应的KafkaMessageListenerContainer，并调用start方法启动监听，也就是这样打通了这条路…

## Spring Boot 自动加载kafka相关配置

### 1、KafkaAutoConfiguration

-   自动生成kafka相关配置，比如当缺少这些bean的时候KafkaTemplate、ProducerListener、ConsumerFactory、ProducerFactory等，默认创建bean实例
    
### 2、KafkaAnnotationDrivenConfiguration

-   主要是针对于spring-kafka提供的注解背后的相关操作，比如 @KafkaListener;
    
-   在开启了@EnableKafka注解后，spring会扫描到此配置并创建缺少的bean实例，比如当配置的工厂beanName不是kafkaListenerContainerFactory的时候，就会默认创建一个beanName为kafkaListenerContainerFactory的实例，这也是为什么在springboot中不用定义consumer的相关配置也可以通过@KafkaListener正常的处理消息

### 生产配置

#### 1、单条消息处理

```java
@Configuration
@EnableKafka
public class KafkaConfig {

  @Bean
  KafkaListenerContainerFactory < ConcurrentMessageListenerContainer < Integer, String >>
    kafkaListenerContainerFactory() {
      ConcurrentKafkaListenerContainerFactory < Integer, String > factory =
        new ConcurrentKafkaListenerContainerFactory < > ();
      factory.setConsumerFactory(consumerFactory());
      factory.setConcurrency(3);
      factory.getContainerProperties().setPollTimeout(3000);
      return factory;
    }

  @Bean
  public ConsumerFactory < Integer, String > consumerFactory() {
    return new DefaultKafkaConsumerFactory < > (consumerConfigs());
  }

  @Bean
  public Map < String, Object > consumerConfigs() {
    Map < String, Object > props = new HashMap < > ();
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, embeddedKafka.getBrokersAsString());
    ...
    return props;
  }
}
@KafkaListener(id = "myListener", topics = "myTopic",
  autoStartup = "${listen.auto.start:true}", concurrency = "${listen.concurrency:3}")
public void listen(String data) {
  ...
}
```

#### 2、批量处理
```java
@Configuration
@EnableKafka
public class KafkaConfig {

  @Bean
  public KafkaListenerContainerFactory < ? , ? > batchFactory() {
    ConcurrentKafkaListenerContainerFactory < Integer, String > factory =
      new ConcurrentKafkaListenerContainerFactory < > ();
    factory.setConsumerFactory(consumerFactory());
    factory.setBatchListener(true); // <<<<<<<<<<<<<<<<<<<<<<<<<
    return factory;
  }

  @Bean
  public ConsumerFactory < Integer, String > consumerFactory() {
    return new DefaultKafkaConsumerFactory < > (consumerConfigs());
  }

  @Bean
  public Map < String, Object > consumerConfigs() {
    Map < String, Object > props = new HashMap < > ();
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, embeddedKafka.getBrokersAsString());
    ...
    return props;
  }
}
@KafkaListener(id = "list", topics = "myTopic", containerFactory = "batchFactory")
public void listen(List < String > list) {
  ...
}
```

### 3、同一个消费组支持单条和批量处理

场景：

> 生产上最初都采用单条消费模式，随着量的积累，部分topic常常出现消息积压，最开始通过新增消费者实例和分区来提升消费端的能力；一段时间后又开始出现消息积压，由此便从代码层面通过批量消费来提升消费能力。

#### 只对部分topic做批量消费处理

简单的说就是需要配置批量消费和单条记录消费(从单条消费逐步向批量消费演进)
-   假设最开始就是配置的单条消息处理的相关配置，原配置基本不变
-   然后新配置 批量消息监听KafkaListenerContainerFactory

```java
@Configuration
@EnableKafka
public class KafkaConfig {

  @Bean(name = [batchListenerContainerFactory])
  public KafkaListenerContainerFactory < ? , ? > batchFactory() {
    ConcurrentKafkaListenerContainerFactory < Integer, String > factory =
      new ConcurrentKafkaListenerContainerFactory < > ();
    factory.setConsumerFactory(consumerFactory());
    // 开启批量处理
    factory.setBatchListener(true);
    return factory;
  }

  @Bean(name = [batchConsumerFactory])
  public ConsumerFactory < Integer, String > consumerFactory() {
    return new DefaultKafkaConsumerFactory < > (consumerConfigs());
  }

  @Bean(name = [batchConsumerConfig])
  public Map < String, Object > consumerConfigs() {
    Map < String, Object > props = new HashMap < > ();
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, embeddedKafka.getBrokersAsString());
    ...
    return props;
  }
}
```


注意点：
-   如果自定义的ContainerFactory其beanName不是kafkaListenerContainerFactory，spring会通过KafkaAnnotationDrivenConfiguration创建新的bean实例，所以需要注意的是你最终的@KafkaListener会使用到哪个ContainerFactory
-   单条或在批量处理的ContainerFactory可以共存，默认会使用beanName为kafkaListenerContainerFactory的bean实例，因此你可以为batch container Factory实例指定不同的beanName，并在@KafkaListener使用的时候指定containerFactory即可