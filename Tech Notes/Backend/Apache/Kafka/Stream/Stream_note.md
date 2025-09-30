
![[Pasted image 20230421135826.png|200]]
![[Pasted image 20230421135905.png|400]]

- 源處理器（Source Processor）：源處理器是一個沒有任何上遊處理器的特殊類型的流處理器。它從一個或多個kafka主題生成輸入流。通過消費這些主題的消息並將它們轉發到下遊處理器。
- Sink處理器：sink處理器是一個沒有下遊流處理器的特殊類型的流處理器。它接收上遊流處理器的消息發送到一個指定的Kafka主題。

Kafka流提供了所謂的狀態存儲，流處理應用程序可以使用它來存儲和查詢數據，這是實現有狀態操作時的一項重要功能。例如，Kafka Streams DSL在調用有狀態操作符(如join()或aggregate())或打開流窗口時自動創建和管理這樣的狀態存儲。

Kafka Streams應用程序中的每個流任務都可以嵌入一個或多個本地狀態存儲，這些存儲可以通過api訪問，以存儲和查詢處理所需的數據。Kafka流為這種本地狀態存儲提供容錯和自動恢復功能。

DATA TYPES AND SERIALIZATION [Ref](https://kafka.apache.org/20/documentation/streams/developer-guide/datatypes)
Every Kafka Streams application must provide SerDes (Serializer/Deserializer) for the data types of record keys and record values (e.g. `java.lang.String`) to materialize the data when necessary.

```java
    /* 1.應用配置參數 */
    Properties props = new Properties();
    // 可作為consumer的group id
    props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-wordcount1");
    // kafka的地址，多個逗號分隔，目前只支持單集群
    props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
    // 序列化和反序列化，在讀取和寫出流的時候、在讀取和寫出state的時候都會用到
    props.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
    props.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());

    /* 2.拓撲 */
    StoreBuilder<KeyValueStore<String, Long>> countStoreSupplier = Stores.keyValueStoreBuilder(
            Stores.persistentKeyValueStore("Counts"),
            Serdes.String(),
            Serdes.Long())
            .withLoggingDisabled(); // disable backing up the store to a changelog topic

    Topology builder = new Topology();

    // add the source processor node that takes Kafka topic "source-topic" as input
    builder.addSource("Source", "source-topic")
            // add the WordCountProcessor node which takes the source processor as its upstream processor
            .addProcessor("Process", WordCountProcessor::new, "Source")
            // add the count store associated with the WordCountProcessor processor
            .addStateStore(countStoreSupplier, "Process")
            // add the sink processor node that takes Kafka topic "sink-topic" as output
            // and the WordCountProcessor node as its upstream processor
            .addSink("Sink", "sink-topic", "Process");

    /* 3.流處理客戶端實例 */
    KafkaStreams streams = new KafkaStreams(builder, props);

    /* 4.啟動 */
    streams.start();
```

[核心知識預熱](https://www.twblogs.net/a/5d44776fbd9eee541c2fe7d9)

stream processing application
多個處理器形成的拓撲結構，包含有一定處理邏輯的應用程序

processor topology
流處理器拓撲，是`processor+...+processor`的形式，source和sink是特殊的processor

Source Processor
源頭處理器，即上遊沒有其他的流處理器，從kafka的topic中消費數據產生數據流輸送到下遊

Sink Processor
結果處理器，即下遊沒有其他的流處理器，將上遊的數據輸送到指定的kafka topic

Time
聯想flink的時間語義，例如某某time1手機端購買某商品，產生了日誌數據，然後time2這個日誌數據被實時采集到Kafka持久化到topic，然後進入流式處理框架，在time3正式被計算，那麼time123分別稱為:event time,ingestion time,processing time

states
保存和查詢數據狀態的功能，可以定義流處理應用外的程序進行只讀訪問

processing guarantees
消費是否丟失和是否重復的級別，比如exactly-once,at-least-once,at-most-once

```java
// DSL轉換算子生成新KStream是調用
void addGraphNode(final StreamsGraphNode parent,final StreamsGraphNode child) {}
// 直接通過builder添加processor
public synchronized Topology addProcessor(final String name,final ProcessorSupplier supplier,final String... parentNames) {}
```

```java
import org.apache.kafka.common.serialization.LongSerializer;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.common.serialization.StringSerializer;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.Topology;

import java.util.Properties;

public class WortCountWithProcessorAPI {
  public static void main(String[] args) {
    Properties properties = new Properties();
    properties.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "gaozhy:9092");
    properties.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
    properties.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());
    properties.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-processor-application");

    // 創建Topology
    Topology topology = new Topology();
    topology.addSource("input", "input");
    topology.addProcessor("wordCountProcessor", () -> new WordCountProcessor(), "input");
    topology.addSink("output", "output", new StringSerializer(), new LongSerializer(), "wordCountProcessor");

    KafkaStreams streams = new KafkaStreams(topology, properties);
    streams.start();
  }
}
```

事實StateStore本質是一個Topic，但是改topic的清除策略不在是delete，而是compact.
關聯StateStore和Processor
```java
// 創建Topology
Topology topology = new Topology();
topology.addSource("input", "input");
topology.addProcessor("wordCountProcessor", () -> new WordCountProcessor(), "input");
// 創建state，存放狀態信息
Map < String, String > changelogConfig = new HashMap();
// override min.insync.replicas
changelogConfig.put("min.insyc.replicas", "1");
changelogConfig.put("cleanup.policy", "compact");
StoreBuilder < KeyValueStore < String, Long >> countStoreSupplier = Stores.keyValueStoreBuilder(
  Stores.persistentKeyValueStore("Counts"),
  Serdes.String(),
  Serdes.Long()).withLoggingEnabled(changelogConfig);

topology.addStateStore(countStoreSupplier, "wordCountProcessor");
topology.addSink("output", "output", new StringSerializer(), new LongSerializer(), "wordCountProcessor");
```

在自定義Processor實現類中使用state
```java
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.processor.Processor;
import org.apache.kafka.streams.processor.ProcessorContext;
import org.apache.kafka.streams.processor.PunctuationType;
import org.apache.kafka.streams.state.KeyValueIterator;
import org.apache.kafka.streams.state.KeyValueStore;

import java.time.Duration;
import java.util.HashMap;

public class WordCountProcessor implements Processor < String, String > {
  private KeyValueStore < String,
  Long > keyValueStore;
  private ProcessorContext context;

  @Override
  public void init(ProcessorContext context) {
    keyValueStore = (KeyValueStore < String, Long > ) context.getStateStore("Counts");
    this.context = context;
    // 定期向下遊輸出計算結果
    this.context.schedule(Duration.ofSeconds(1), PunctuationType.STREAM_TIME, timestamp -> {
      System.out.println("----------- " + timestamp + " ----------- ");
      KeyValueIterator < String,
      Long > iterator = keyValueStore.all();
      while (iterator.hasNext()) {
        KeyValue < String, Long > entry = iterator.next();
        this.context.forward(entry.key, entry.value);
      }
      iterator.close();
    });
  }

  @Override
  public void process(String key, String value) {
    String[] words = value.split(" ");
    for (String word: words) {
      Long oldValue = keyValueStore.get(word);
      if (oldValue == null) {
        keyValueStore.put(word, 1 L);
      } else {
        keyValueStore.put(word, oldValue + 1 L);
      }
    }
    context.commit();
  }

  @Override
  public void close() {

  }
}
```

[大全Kafka Streams](https://www.cnblogs.com/huzixia/p/10384101.html)
6.3 本地狀態倉庫  
處理器API不僅可以處理當前到達的記錄，也可以管理本地狀態倉庫以使得已到達的記錄都可用於有狀態的處理操作中（如聚合或開窗連接）。為利用本地狀態倉庫的優勢，可使用TopologyBuilder.addStateStore方法以便在創建處理器拓撲時創建一個相應的本地狀態倉庫；或將一個已創建的本地狀態倉庫與現有處理器節點連接，通過TopologyBuilder.connectProcessorAndStateStores方法。

```java
TopologyBuilder builder = new TopologyBuilder();

builder.addSource("SOURCE", "src-topic")

.addProcessor("PROCESS1", MyProcessor1::new, "SOURCE")
// create the in-memory state store "COUNTS" associated with processor "PROCESS1"
.addStateStore(Stores.create("COUNTS").withStringKeys().withStringValues().inMemory().build(), "PROCESS1")
.addProcessor("PROCESS2", MyProcessor3::new /* the ProcessorSupplier that can generate MyProcessor3 */, "PROCESS1")
.addProcessor("PROCESS3", MyProcessor3::new /* the ProcessorSupplier that can generate MyProcessor3 */, "PROCESS1")

// connect the state store "COUNTS" with processor "PROCESS2"
.connectProcessorAndStateStores("PROCESS2", "COUNTS");

.addSink("SINK1", "sink-topic1", "PROCESS1")
.addSink("SINK2", "sink-topic2", "PROCESS2")
.addSink("SINK3", "sink-topic3", "PROCESS3");
```

```java

StateStoreSupplier fastStore = Stores.create("FAST-store").withStringKeys().withDoubleValues().inMemory().build();

.addProcessor("FAST-processor",() -> new MovingAverageProcessor(0.1),"messages-source")
.addStateStore(fastStore, "FAST-processor")


```