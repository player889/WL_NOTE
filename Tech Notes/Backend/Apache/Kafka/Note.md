## Brief

![[Pasted image 20230421115032.png]]
![[Pasted image 20230421151456.png]]

主流的消息引擎系，比如ActiveMQ 、RabbitMQ （ 通過RabbitMQ JMS Client ）、IBM WebSphere MQ 和Kafka 等。
ZooKeeper 是為Kafka 提供協調服務的工具
Kafka 社區推出了一個全新的流式處理組件Kafka Streams
老牌流式處理框架Apache Storm 、Apache Samza、Spark Strearnirg和Apache Flink
Kafka 非常適合替代傳統的消息總線（ message bus ）或消息代理（ message broker ）
每次寫入操作其實都只是把數據寫入到操作系統的頁緩存（ page cache ）中，然後由操作系統自行決定什麽時候把頁緩存中的數據寫回磁盤上。
	- 大量使用操作系統頁緩存，內存操作速度快且命中率高。
	- Kafka 不直接參與物理I/0 操作，而是交由最擅長此事的操作系統來完成。
	- 采用追加寫入方式，摒棄了緩慢的磁盤隨機讀／寫操作。
	- 使用以sendfile 為代表的零拷貝技術加強網絡間的數據傳輸效率。

內存規劃
- 盡量分配更多的內存給操作系統的page cache 。
- 不要為broker 設置過大的堆內存，最好不超過6GB 。
- page cache 大小至少要大於一個日誌段的大小。

CPU 規劃
- 使用多核系統， CPU 核數最好大於8 。
- 如果使用Kafka 0.10 . 0.0 之前的版本或clients 端與broker 端消息版本不一致（若無顯式配置，這種情況多半由clients 和broker 版本不一致造成），則考慮多配置一些資源以防止消息解壓縮操作消耗過多CPU 。

帶寬規劃
- 盡量使用高速網絡。
- 根據自身網絡條件和帶寬來評估Kafka 集群機器數量。
- 避免使用跨機房網絡。

---

[APACHE KAFKA](https://kafka.apache.org/) 是一個分散式的messaging system，裡面主要有三個角色，broker，producer，consumer。
producer是生產者，負責生產message並傳送至broker(中間人、代理者)，consumer則是消費者負責拉走message。
1. 生產者發送消息給Kafka 服務器。
2. 消費者從Kafka 服務器讀取消息。
3. Kafka 服務器依托ZooKeeper 集群進行服務的協調管理。

Kafka的broker設定愈多，能處理的資料量也會隨之擴展。在建立topic時，可以選擇這個topic要有幾個partition。例如: 我們先設定了3個broker，建立一個topic名叫news，並設定這個topic的partition為6。那麼producer生產message至news這個topic時，會依序進入這6個partiton。通常若不調整設定，則每個broker會負責news topic的2個partition。

想像每個broker都是一個文件處理人員，我們目前有3個處理人員，現在將news的所有文件分為6份(也就是一個叫做news的topic，設定為6個partition)，所以每個處理人員都分配到2份文件。當有新的news文件出現時，會依照規則放在partition 0~5 的位置，每個partition都是依序處理。(所以，當partition愈多，能處理的效能愈快，最好能夠是broker的倍數，才能讓broker達到較好的效能。)

![[Pasted image 20230421114630.png|400]]

![[Pasted image 20230302101217.png|400]]

分布式系統必然要實現高可靠性，而目前實現的主要途徑還是依靠冗余機制一一簡單地說，就是備份多份日誌。這些備份日誌在Kafka 中被稱為副本（ replica ），它們存在的唯一目的就是防止數據丟失。副本分為兩類： 領導者副本（ leader replica ）和追隨者副本（ follower replica ） 。follower replica 是不能提供服務給客戶端的，也就是說不負責響應客戶端發來的消息寫入和消息消費請求。它只是被動地向領導者副本（ leader replica ）獲取數據，而一旦leader replica 所在的broker 巖機， Kafka 會從剩余的replica 中選舉出新的leader 繼續提供服務。

ISR 的全稱是in-sync replica，翻譯過來就是與leader replica 保持同步的replica 集合。

```java
Properties props= new Properties();
props.put("bootstrap.servers","localhost:9092");
props.put("acks","all");
props.put("retries", 0);
props.put("batch.size", 16384);
props.put("linger.ms", l) ;
props.put("buffer.memory", 33554432);
props.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
Producer<String, String> producer = new KafkaProducer<>(props);
for(int i = O; i < 100; i++）
	producer.send(new ProducerRecord<String, String>("my-topic"，integer.toString(i), Integer.toString(i)));
producer.close();
```

```java
//1.構造Properties對象
Properties props= new Properties();
props.put("bootstrap.servers" ,"localhost:9092");
props.put("group.id","test");
props.put("enable.auto.commit","true");
props.put("auto.commit.interval.ms","l00"）；
props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
//2.使用Properties構造KafkaConsumer對象
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
//3.調用KafkaConsumer的subscribe方法訂閱consumer group感興趣的topic列表
consumer.subscribe(Arrays.asList("foo","bar"))；
try{
	while (true) {
		 //4.調用conusmer.poll方法   獲取封裝在consumerRecord中的消息
		ConsumerRecords<String , String> records = consumer.poll(l00);
		for (ConsumerRecord<String, String> record : records)
			//5.處理獲取到的consumerRecord對象
			System.out.printf("offset=%d,key=%s,value=%s%n", record.offset(), record.key(), record.value());
	}
}finally{
	//6.關閉cnsumer
	consumer.close();
}
```

- poll ：最重要的方法。它是實現讀取消息的核心方法。
- subscribe ：訂閱方法，指定consumer 要消費哪些topic 的哪些分區。
- commitSync/commitAsync ：手動提交位移方法。新版本consumer 允許用戶手動提交位移，並提供了同步/異步兩種方式
- seek/seekToBeginning/seekToEnd ：設置位移方法。除了提交位移， consumer 還可以直接消費特定位移處的消息。

和Hadoop 、HBase 這樣的大多數分布式框架類似， Kafka 集群搭建也分為單節點的偽分布式集群和多節點的分布式集群兩種方式

安裝多節點ZooKeeper 集群, 多節點環境配置文件zoo.cfg：

- tickTime: ZooKeeper 最小的時間單位，用於丈量心跳時間和超時時間等。通常設置成默認值2 秒即可。
- dataDir ：非常重要的參數！ ZooKeeper 會在內存中保存系統快照，井定期寫入該路徑指定的文件夾中。生產環境中需要註意該文件夾的磁盤占用情況。
- ctientPort: ZooKeeper 監昕客戶端連接的端口， 一般設置成默認值2181 即可。
- initLimit ：指定follower 節點初始時連接leader 節點的最大tick 次數。假設是5 ， 表示follower 必須要在5 × tickTime 時間內（默認是10 秒）連接上leader ，否則將被視為超時。
- syncLimit ：設定了follower 節點與leader 節點進行同步的最大時間。與initLimit 類似，它也是以tickTirne 為單位進行指定的。
- server.X=host:port:port ：配置文件中的最後3 行都是這種形式的。這裏的X 必須是一個全局唯一的數字，且需要與myid 文件中的數字相對應（關於myid 文件的設置稍後會做詳細討論）。一般設置X 值為l ～255 之間的整數。這行的後面還配置了兩個端口，通常是2888 和3888 。第一個端口用於使follower 節點連接leader 節點，而第二個端口則用於leader 選舉。

每個ZooKeeper 服務器都有一個唯一的ID 。這個ID 主要用在兩個地方：一個就是剛剛我們配置的zoo.cfg 文件，另一個則是myid 文件。myid 文件位於zoo.cfg運中dataDir 配置的目錄下，其內容也很簡單，僅是一個數字，即ID 。

創建好ZooKeeper 配置文件，下一步就是創建myid 文件了。myid 文件必須位於配置文件中的dataDir 中， 即 /mnt/disk/huxitest/data _logs/zookeeper 1 ,2,3下，

```
echo " 1 "> /mnt/disk/huxitest/data logs/zookeeperl/myid
echo " 2 "> /mnt/disk/huxitest/data logs/zookeeper2/myid
echo " 3 "> /mnt/disk/huxitest/data logs/zookeeper3/myid
```

安裝多節點Kafka

搭建Kafka 集群的第一步是要創建多份配置文件

在上面3 個配置文件中我們需要每台Kafka 服務器指定不同的broker ID 。該由在整個集群中必須是唯一的。而配置listeners 時最好使用節點的FQDN (Fully Qualified Domain Name) ,即全稱域名，盡量不要使用地址。另外配置文件中還有一個特別重要的參數，即zookeeper.connect 。鑒於我們之前己經搭建了一個3 節點Zoo Keeper 集群，此時配置zookeeper.connect 必須同時指定所有的ZooKeeper 節點。註意本例中使用的端口分別是8001 、8002 和8003 。

創建Kafka 配置文件之後，剩下的工作就很簡單了，只需要執行下列命令啟動Kafkabroker 服務器：

```command
bin/kafka-server-start.sh -daemon config/serverl.properties
bin/kafka-server-start.sh -daemon config/server2.properties
bin/kafka-server-start.sh -daemon config/server3.properties
```

Kafka 集群涉及的各方面參數，主要包括以下幾種參數。
- broker 端參數。
- topic 級別參數。
- GC 配置參數。
- OS 參數
- ...

---

Kafka是一個分佈式流資料系統，使用Zookeeper進行叢集的管理。與其他消息系統類似，整個系統由生產者、Broker Server和消費者三部分組成，生產者和消費者由開發人員編寫，通過API連接到Broker Server進行資料操作。我們重點關注三個概念：

**Topic**
是Kafka下消息的類別，類似於RabbitMQ中的Exchange的概念。這是邏輯上的概念，用來區分、隔離不同的消息資料，遮蔽了底層複雜的儲存方式。對於大多數人來說，在開發的時候只需要關注資料寫入到了哪個topic、從哪個topic取出資料。 

**Partition**
是Kafka下資料儲存的基本單元，這個是物理上的概念。*==同一個topic的資料，會被分散的儲存到多個partition中，這些partition可以在同一台機器上，也可以是在多台機器上==*，比如topic就有4個partition，分散在兩台機器上。這種方式在大多數分佈式儲存中都可以見到，比如MongoDB、Elasticsearch的分片技術，其優勢在於：有利於水平擴展，避免單台機器在磁碟空間和性能上的限制，同時可以通過複製來增加資料冗餘性，提高容災能力。為了做到均勻分佈，通常partition的數量通常是Broker Server數量的整數倍。

**Consumer Group**，同樣是邏輯上的概念，是Kafka實現單播和廣播兩種消息模型的手段。同一個topic的資料，會廣播給不同的group；同一個group中的worker，只有一個worker能拿到這個資料。*==換句話說，對於同一個topic，每個group都可以拿到同樣的所有資料，但是資料進入group後只能被其中的一個worker消費。==*group內的worker可以使用多執行緒或多處理程序來實現，也可以將處理程序分散在多台機器上，worker的數量通常不超過partition的數量，且二者最好保持整數倍關係，因為Kafka在設計時假定了一個partition只能被一個worker消費（同一group內）。

---
## Producer
```ad-abstract
title: Producer
![[Pasted image 20230302112856.png|200]]


1. 構造一個java.util.Properties，然後至少指定bootstrap.servers、key.serializer和value.serializer，它們沒有默認值。
	- 在創建 ProducerRecord 時，必須指定序列化器，除了默認提供的序列化器之外推薦使用序列化框架 Avro、Thrift、ProtoBuf 等。
	- 用戶可以為消息的key 和value 指定不同類型的serializer，只要與解序列類型分別保持一致就可以。
	- 若要編寫一個自定義的serializer,需要完成以下3件事情
		1. 定義數據對象格式
		2. 創建自定義序列化類，實現org.apache.kafka.common.serialization.Serializer接口，在serializer方法中實現序列化邏輯
		3. 在用於構造KafkaProducer的Properties對象中設置key.serializer或value.serializeru取決於是為消息key還是value做自定義序列化
1. 使用Properties，new KafkaProducer對象
2. 構造待發送的消息對象ProducerRecord，指定消息要被發送到的topic、分區以及對應的key和value
   分區和key信息可以不用指定，由Kafka自行確定目標分區
4. 調用KafkaProducer的send方法發送消息
5. 關閉KafkaProducer

~~~java
public class ProducerTest {
    public static void main(String[) args) throws Exception {
            Properties props = new Properties();
			//必須指定
            props.put（ "bootstrap.servers"，"localhost:9092"）；
            //必須指定
            props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer"）；
			//必須指定
			props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer"）；
			props.put("acks", "-1");
			props.put("retries", 3);
			props.put("batch.size", 323840);
			props.put("linger.ms", 10);
			props.put("buffer.memory", 33554432);
			props.put("max.block.ms", 3000);
			Producer < String, String > producer = new KafkaProducer<>(props);
			for (int i = 0; i < 100; i++)
				producer.send(new ProducerRecord<>("my-topic ", Integer.toString(i), Integer.toString(i))); producer.close();
	}
}
~~~

Java版本producer的send方法會返回一個Java Future對象供用戶稍後獲取發送結果，這就是所謂的回調機制。
send方法提供了回調類參數來實現異步發送以及對發送結果的響應，具體代碼如下：
~~~java
producer.send(record, new Callback() {
	@Override
	public void onCornpletion(RecordMetadata rnetadata, Exception exception) {
		if (exception == null) {
		//消息發送成功
		} else {
		//執行錯誤處理邏輯
		}
	}
});
~~~
上面代碼中的Callback就是發送消息後的回調類，實現方法是onCompletion。該方法的兩個輸入參數metadata和exception不會同時非空，也就是說至少有一個是null。當消息發送成功時，exception是null；反之，若消息發送失敗，metadata就是null。因此在寫producer程序時，最好寫if語句進行判斷。另外，上面的Callback實際上是一個Java接口，用戶==可以創建自定義的Callback實現類來處理消息發送後的邏輯，只要該具體類實現org.apache.kafka.clients.producer.Callback接口即可。==

- LeaderNotAvailableException：分區的leader副本不可用，這通常出現在leader換屆選舉期間，因此通常是瞬時的異常，重試之後可以自行恢復。
- NotControllerException:controller當前不可用。這通常表明controller在經歷新一輪的選舉，這也是可以通過重試機制自行恢復的。
- NetworkException：網絡瞬時故障導致的異常，可重試。
- RecordTooLargeException：發送的消息尺寸過大，超過了規定的大小上限。顯然這種異常無論如何重試都是無法成功的。
- SerializationException：序列化失敗異常，這也是無法恢復的。
- KafkaException：其他類型的異常

producer 主要參數

1. acks參數用於控制producer生產消息的持久性（durability）
	- acks=0：設置成0表示producer完全不理睬leaderbroker端的處理結果。此時，producer發送消息後立即開啟下一條消息的發送，根本不等待leaderbroker端返回結果。由於不接收發送結果，因此在這種情況下producer.send的回調也就完全失去了作用，即用戶無法通過回調機制感知任何發送過程中的失敗，所以acks=O時producer並不保證消息會被成功發送。但凡事有利就有弊，由於不需要等待響應結果，通常這種設置下producer的吞吐量是最高的
	- acks=all或者-1：表示當發送消息時，leaderbroker不僅會將消息寫入本地日誌，同時還會等待ISR中所有其他副本都成功寫入它們各自的本地日誌後，才發送響應結果給producer。顯然當設置acks=all時，只要ISR中至少有一個副本是處於“存活"狀態的，那麽這條消息就肯定不會丟失，因而可以達到最高的消息持久性，但通常這種設置下producer的吞吐量也是最低的。
	- acks=1：是0和all折中的方案，也是默認的參數值。producer發送消息後leaderbroker僅將該消息寫入本地日誌，然後便發送響應結果給producer，而無須等待ISR中其他副本寫入該消息。那麽此時只要該leaderbroker一直存活，Kafka就能夠保證這條消息不丟失。這實際上是一種折中方案，既可以達到適當的消息持久性，同時也保證了producer端的吞吐量。
1. buffer.memoy該參數指定了producer端用於緩存消息的緩沖區大小，單位是字節，默認值是33554432,即32MB。
2. compression.type參數設置producer端是否壓縮消息，默認值是none，即不壓縮消息
3. Kafka broker在處理寫入請求時可能因為瞬時的故障（比如瞬時的leader選舉或者網絡抖動〉導致消息發送失敗。這種故障通常都是可以自行恢復的，如果把這些錯誤封裝進回調函數的異常中返還給producer,producer程序也並沒有太多可以做的，只能簡單地在回調函數中重新嘗試發送消息。與其這樣，還不如producer內部自動實現重試。因此Java版本producer在內部自動實現了重試，當然前提就是要設置re仕ies參數。
4. batch.size是producer最重要的參數之一！它對於調優producer吞吐量和延時性能指標都有著非常重要的作用。前面提到過，producer會將發往同一分區的多條消息封裝進一個batch巾。當batch滿了的時候，producer會發送batch中的所有消息。不過，producer並不總是等待batch滿了才發送消息，很有可能當batch還有很多空閑空間時producer就發送該batch。batch.size參數默認值是16384，即16KB
5. linger.msinger.ms參數就是控制消息發送延時行為的。該參數默認值是0，表示消息需要被立即發送，無須關心batch是否己被填滿，大多數情況下這是合理的，畢竟我們總是希望消息被盡可能快地發送。不過這樣做會拉低producer吞吐量，畢竟producer發送的每次請求中包含的消息數越多，producer就越能將發送請求的開銷攤薄到更多的消息上，從而提升吞吐量。
6. max.request.size該參數控制的是producer端能夠發送的最大消息大小
7. request.timeout.ms當producer發送請求給broker後，broker需要在規定的時間範圍內將處理結果返還給producer。這段時間便是由該參數控制的，默認是30秒。這就是說，如果broker在30秒內都沒有給producer發送響應，那麽producer就會認為該請求超時了，並在回調函數中顯式地拋出TimeoutException異常交由用戶處理。

若要使用自定義分區機制，用戶需要完成兩件事情
1. 在producer 程序中創建一個類，實現org.apache.kafka.clients.producer.Partitioner 接口。主要分區邏輯在Partitioner.partition 中實現。
2. 在用於構造KafkaProducer 的Properties 對象中設置partitioner.class 參數
~~~java
/***
 * 實現一個自定義分區策略：
 * key含有audit的一部分消息發送到最後一個分區上，其他消息在其他分區隨機分配
 */
public class PartitionerImpl implements Partitioner {

    private Random random;

    public void configure(Map<String, ?> configs) {
        //做必要的初始化工作
        random = new Random();
    }

    //分區策略
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {

        String keyObj = (String) key;
        List<PartitionInfo> partitionInfoList = cluster.availablePartitionsForTopic(topic);
        int partitionCount = partitionInfoList.size();
        System.out.println("partition size: "  + partitionCount);
        int auditPartition = partitionCount - 1;
        return keyObj == null || "".equals(keyObj) || !keyObj.contains("audit") ? random.nextInt(partitionCount - 1) : auditPartition;
    }

    public void close() {
        //清理工作
    }
}
~~~

~~~java
//實現類
properties.put(ProducerConfig.PARTITIONER_CLASS_CONFIG,"cn.org.fubin.PartitionerImpl");
~~~

發送一次audit 消息，其他消息分別發送兩次。

~~~java
String topic = "audit-test";
Producer<String,String> producer = new KafkaProducer<String, String>(properties);
ProducerRecord nonKeyRecord = new ProducerRecord(topic,"non-key record");
//這類消息需要放在最後一個分區
ProducerRecord auditRecord = new ProducerRecord(topic,"audit","audit record");
ProducerRecord nonAuditRecord = new ProducerRecord(topic,"other","non-audit record");

try {
    producer.send(nonAuditRecord).get();
    producer.send(nonAuditRecord).get();
    producer.send(auditRecord).get();
    producer.send(nonAuditRecord).get();
    producer.send(nonAuditRecord).get();
} catch (InterruptedException e) {
    e.printStackTrace();
} catch (ExecutionException e) {
    e.printStackTrace();
}
~~~

```

producer 攔截器 - interceptor 使得用戶在消息發送前以及producer 回調邏輯前有機會對消息做一些定制化需求，比如修改消息等。
intercetpor 的實現接口是org.apache.kafka.clients.producer.Producerlnterceptor ，其定義的方法如下。
1. onSend(Producer Record）：該方法封裝進KafkaProducer.send 方法中，即它運行在用戶主線程中。producer 確保在消息被序列化以計算分區前調用該方法。用戶可以在該方法中對消息做任何操作，但最好保證不要修改消息所屬的topic 和分區，否則會影響目標分區的計算。
2. onAcknowledgement(RecordMetadata, Exception）：該方法會在消息被應答之前或消息發送失敗時調用，井且通常都是在producer 回調邏輯觸發之前。onAcknowledgement 運行在producer 的1/0 線程中，因此不要在該方法中放入很“重"的邏輯，否則會拖慢producer 的消息發送效率。
3. close ：關閉interceptor， 主要用於執行一些資源清理工作。
若指定了多個interceptor ，則producer 將按照指定順序調用它們，同時把每個interceptor 中捕獲的異常記錄到錯誤日誌中而不是向上傳遞

把時間戳寫入消息體的最前部。
```java
public class TimeStampPrependerInterceptor implements ProducerInterceptor<String, String> {
    @Override
    public void configure(Map<String, ?> configs) {
 
    }
 
    @Override
    public ProducerRecord onSend(ProducerRecord record) {
        return new ProducerRecord(
                record.topic(), record.partition(), record.timestamp(), record.key(), System.currentTimeMillis() + "," + record.value().toString());
    }
 
    @Override
    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
 
    }
 
    @Override
    public void close() {
    }
}
```


下面定義第二個interceptor - Counterlnterceptor ，該interceptor 會在消息發送後更新“發送成功消息數"和“發送失敗消息數"兩個計數器，並在producer 關閉時打印這兩個計數器。
```java
public class Counterinterceptor implements Producerinterceptor < String, String > (
    private int errorCounter = 0; private int successCounter = 0;

    @Override public void configure(Map < String, ? > configs)(
    }

    @Override public ProducerRecord < String, String > onSend(ProducerRecord < String, String > record)(
        return record;

    }
    @Override public void onAcknowledgement(RecordMetadata metadata, Exception exception) {

        if (exception == null) {
            successCounter++;
        } else {
            errorCounter++;
        }
    }
    @Override public void close() {
    ／／保存結果
        System.out.println("Successful sent: "+successCounter);
        System.out.println("Failed sent: "+errorCounter);
    }

}
```

```java

Properties props = new Properties();
props.put(...);
// 構建攔截鏈
List<String> interceptors = new ArrayList<>();
// interceptor 1
interceptors.add("huxi.test.producer.TimeStampPrependerInterceptor");
// interceptor 2
interceptors.add("huxi.test.producer.CounterInterceptor");
props.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG, interceptors);
...
 
String topic = "test-topic";
Producer<String, String> producer = new KafkaProducer<>(props);
for (int i = 0; i < 10; i++) {
    ProducerRecord<String, String> record = new ProducerRecord<>(topic, "message" + i);
    producer.send(record).get();
}
 
// 一定要關閉producer，這樣才會調用interceptor的close方法
producer.close();
```

Java 版本producer 用戶采用異步發送機制。KafkaProducer.send 方法僅僅把消息放入緩沖區中，由一個專屬1/0 線程負責從緩沖區中提取消息井封裝進消息batch 中，然後發送出去。顯然，這個過程中存在著數據丟失的窗口：若I/0 線程發送之前producer 崩潰，則存儲緩沖區中的消息全部丟失了。這是producer 需要處理的很重要的問題。

producer 的另一個問題就是消息的亂序
```java
producer.send(recordl);
producer.send(record2);
```
若此時由於某些原因（比如瞬時的網絡抖動〉導致record1未發送成功，同時Kafka 又配置了重試機制以及max.in.flight.requests.per.connection 大於1 (默認值是5 )，那麽producer 重試record1 成功後， record 1在日誌中的位置反而位於record2 之後，這樣造成了消息的亂序。要知道很多實際使用場景中都有事件強順序保證的要求。

采用同步發，但是性能會很差，並不推薦在實際場景中使用
```java
producer.send(record) . get();
```

producer 端“無消息丟失配置"
- block.on.buffer.full ＝true
> 實際上這個參數在Kafka 0.9.0.0 版本已經被標記為“ deprecated "，並使用max.block.ms 參數替代，但這裏還是推薦用戶顯式地設置它為true ，使得內存緩沖區被填滿時producer 處於 阻塞狀態並停止接收新的消息而不是拋出異常；否則producer 生產速度過快會耗盡緩沖區。新 版本Kafka ( 0.10.0.0 之後）可以不用理會這個參數，轉而設置max.block.ms 即可。
-  acks = all or 1
- retries = Integer.MAX_ VALUE
> 設置成MAX_VALUE 縱然有些極端，但其實想表達的是producer 要開啟無限重試。用戶 不必擔心pro ducer 會重試那些肯定無法恢復的錯誤，當前producer 只會重試那些可恢復的異常 情況，所以放心地設置一個比較大的值通常能很好地保證消息不丟失。
- max.in.flight.requests.per.connection= 1
> 設置該參數為1 主要是為工防止t opic 同分區下的消息亂序問題。這個參數的實際效果其 實限制了producer 在單個broker 連接上能夠發送的未響應請求的數量。因此，如果設置成l , 則producer 在某個broker 發送響應之前將無法再給該broker 發送PRODUCE 請求。
- 使用帶回調機制的send 發送消息，即KafkaPro ducer.send(record, callback)
>不要使用KafkaPro ducer 中單參數的send 方法，因為該se nd 調用僅僅是把消息發出而不會理會消息發送的結果。如果消息發送失敗，該方法不會得到任何通知，故可能造成數據的丟失。 實際環境中一定要使用帶回調機制的send 版本，即KafkaProducer.send(record, callback） 。
- Callback 邏輯中顯式地立即關閉producer ，使用close(O)
>在Callback 的失敗處理邏輯中顯式調用KafkaProducer .close(O ） 。這樣做的目的是為了處理 消息的亂序問題。若不使用close(O），默認情況下producer 會被允許將未完成的消息發送出去， 這樣就有可能造成消息亂序。

broker 端配置“無消息丟失配置"
- unclean.leader.election .enable= false
>關閉unclean leader 選舉，即不允許非ISR 中的副本被選舉為leader ，從而避免broker 端因日誌水位截斷而造成的消息丟失。
- replication.factor = 3
>設置成3 主要是參考了Hadoop 及業界通用的三備份原則，其實這裏想強調的是一定要使用多個副本來保存分區的消息。
- min.insync .replicas = 2
>用於控制某條消息至少被寫入到ISR 中的多少個副本才算成功，設置成大於1 是為了提升producer 端發送語義的持久性。記住只有在producer 端acks 被設置成all 或一l 時，這個參數才有意義。在實際使用時，不要使用默認值。
- replication.factor > min.insync.replicas
>若兩者相等，那麽只要有一個副本掛掉，分區就無法正常工作，雖然有很高的持久性但可用性被極大地降低了。推薦配置成replication.factor= min.insyn.replicas + l 。
- enable .auto.commit= false


Kafka 壓縮特性的話，那麽就是 : producer 端壓縮， broker 端保持， consumer 端解壓縮。所謂的broker 端保持是指broker 端在通常情況下不會進行解壓縮操作，它只是原樣保存消息而己。

KatkaProducer 是線程安全的

## Conumser

```ad-abstract
title: Consumer

消費者（ consumer)
- 消費者組（ consumer group ）- 由多個消費者實例（ consumer instance ）構成一個整體進行消費的
- 獨立消費者（ standalone consumer ） - 單獨執行消費操作。

1. 一個consumer group 可能有若幹個consumer實例（ 一個group 只有一個實例也是允許的）
2. 對於同一個group 而言， topic 的每條消息只能被發送到group 下的一個consumer 實例上：
3. topic 消息可以被發送到多個group 中。

- 所有consumer 實例都屬於相同group一一實現基於隊列的模型。每條消息只會被一個consumer 實例處理。
- consumer 實例都屬於不同group一－實現基於發布/訂閱的模型。極端的情況是每個consumer 實例都設置完全不同的group ，這樣Kafka 消息就會被廣播到所有consumer實例上。

consumer group 是用於實現高伸縮性、高容錯性的consumer 機 制。組內多個consumer 實例可以同時讀取Kafka 消息，而且一旦有某個consumer “掛"了，consumer group 會立即將己崩潰consumer 負責的分區轉交給其他consumer 來負責，從而保證整個group 可以繼續工作，不會丟失數據 - 這個過程被稱為重平衡（ rebalance ）

- consumer group 下可以有一個或多個consumer 實例。一個consumer 實例可以是一個線程，也可以是運行在其他機器上的進程。
- group.id 唯一標識一個consumer group 。
- 對某個group 而言，訂閱topic 的每個分區只能分配給該group 下的一個consumer 實例（當然該分區還可以被分配給其他訂閱該topic 的消費者組） 。


位移（offset） 。很多消息引擎都把消費端的offset 保存在服務器端（ broker ） 。這樣做的好處當然是實現簡單，但會有以下3 個方面的問題。
- broker 從此變成了有狀態的，增加了同步成本， 影響伸縮性。
- 需要引入應答機制（ acknowledgement ）來確認消費成功。
- 由於要保存許多consumer 的offset ，故必然引入復雜的數據結構，從而造成不必要的資源浪費。

~~~java
public class ConsumerTest {
    public static void main(String[] args) {
            String topicName = "test - topic";
            String groupID = "test - group";
            Properties props = new Properties();
			//必須指定
            props.put（" bootstrap.servers"，" localhost: 9092"）；
			//必須指定
            props.put("group.id", group ID); 
            props.put（" enable.auto.comm 工t"，" true"）；
            props.put("auto.commit.interval.ms", "100"）； 
			//從最早的消息開始讀取
            props.put（" auto.offset.reset","earliest"）；
			//必須指定
            props.put("key.deserializer", "org.apache.kafka.common.serialization.Str工ngDeserializer"）;
			//必須指定
            props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer"）；
			//創建consumer 實例
            KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
			//訂閱topic
            consumer.subscribe(Arrays.asList(topicName））；
			try {
				while (true) {
					ConsumerRecords < String, String > records = consumer.poll(l000);
					for (ConsumerRecord < String, String > record: records)
						System.out.printf("offset＝ %d, key＝ %s, value＝ %s %n"， record.off set(), record.key(), record.value());
				} finally {
					consumer.close();
				}
		}
	}
}
~~~

1. 構造一個java. util.Properties 對象，至少指定bootstrap. servers 、key.deserializer 、value.deserializer 和group.id 的值，即上面代碼中顯式指明必須指定帶註釋的4 個參數。
2. 使用上一步創建的Properties 實例構造Kafka Consumer 對象。
3. 調用KafkaConsumer.subscribe 方法訂閱consumer group 感興趣的topic 列表。
4. 循環調用KafkaConsumer.poll 方法獲取封裝在ConsumerRecord 的topic 消息。處理獲取到的ConsumerRecord 對象。
5. 關閉KafkaConsumer 。

group.id - 該參數指定的是consumer group 的名字。它能夠唯一標識一個consumer group 
key.deserializer - consumer代碼從broker端獲取的任何消息都是字節數組的格式，因此消息的每個組件都要執行相應的解序列化操作才能還原成原來的對象格式
value.deserializer - 與value.deserializer 類似，該參數被用來對消息體（即消息value ）進行解序列化，從而把消息“還原"回原來的對象類型

則表達式。假設consumer group 要消費所有以kafka 開頭的topic
~~~java
consumer.subscribe(Pattern.compile("kafka.*"), newNoOpConsumerRebalanceListener());
~~~


上面代碼中最關鍵的調用方法當屬consumer.poll( 1000） 。這裏的1000 是一個超時設定 ( timeout ） 。通常情況下如果consumer 拿到了足夠多的可用數據，那麽它可以立即從該方法 返回；但若當前沒有足夠多的數據可供返回， consumer 會處於阻塞狀態。這個超時參數即控制 阻塞的最大時間。這裏的1000 表示即使沒有那麽多數據， consumer 最多也不要等待超過l 秒 的時間。

這個超時設定給予了用戶能夠在consumer 消費間隔之余做一些其他事情的能力。若用戶 有定時方面的需求，那麽根據需求設定timeout 是一個不錯的選擇。否則，設定一個比較大的 值甚至Integer.MAX_VALUE ，是不錯的建議。

- session . timeout.ms
> session. timeout.ms 是consumer group 檢測組內成員發送崩潰的時間。session. timeout.ms 參數被明確為 “ coordinator 檢測失敗的時間" 。因此在實際使用中，用戶可以為該參數設置一個比較小的值 讓coordinator 能夠更快地檢測consumer 崩潰的情況，從而更快地開啟rebalance ，避免造成更 大的消費滯後（ consumer lag ） 。目前該參數的默認值是10 秒。
- max.poll.interval.ms 
> consumer 處理邏輯最大時間。假設用戶的業務場景中消息處理邏輯是把消息、“落地"到遠程數據庫中，且這個 過程平均處理時間是2 分鐘，那麽用戶僅需要將max.poll.interval.ms 設置為稍稍大於2 分鐘的值 即可，而不必為session. neout.ms 也設置這麽大的值。通過將該參數設置成實際的邏輯處理時間再結合較低的session. timeout.ms 參數值， consumer group 既實現了快速的consumer 崩潰檢測，也保證了復雜的事件處理邏輯不會造成不 必要的rebalance 。
- auto. offset. reset
> - earliest ：指定從最早的位移開始消費。註意這裏最早的位移不一定就是0 。
> - latest ：指定從最新處位移開始消費。
> - none ：指定如果未發現位移信息或位移越界，則拋出異常。筆者在實際使用過程中幾乎從未見過將該參數設置為none 的用法，因此該值在真實業務場景中使用甚少。
- enable.auto.commit
> 對於有較強“精確處理一次"語義需求的用戶來說，最好將該參數設置為false ，由用戶自行處理位移提交問題。
- fetch.max.bytes
> consumer 端單次獲取數據的最大字節數。若實際業務消息很大，則必須要設置該參數為一個較大的值，否則consumer 將無法消費這些消息。
- max.poll.records
> 該參數控制單次poll 調用返回的最大消息數。
- heartbeat.interval.ms
- connections.max.idle.ms
> 經常有用戶抱怨在生產環境下周期性地觀測到請求平均處理時間在飄升，這很有可能是因為Kafka 會定期地關閉空閑Socket 連接導致下次consumer 處理請求時需要重新創建連向broker 的Socket 連接。比較推薦設置該參數值為－ 1

consumer group 訂閱topic列表
~~~java
consumer.subscribe(Arrays . asList ( " topicl ","topic2 ","topic3 "));
~~~

consumer(standalone consumer）
~~~java
TopicPartition tpl =new TopicPartition (" topic-name ", 0) ;
T picPartition tp2 =new TopicPartition (" topic - name ", 1) ;
consumer.assign(Arrays . asList(tpl , tp2)) ;
~~~

`ris:Chrome`[kafka系列之Consumer 消費模式(10)](https://blog.csdn.net/king14bhhb/article/details/114702895)

consumer 訂閱topic 之後通常以事件循環的方式來獲取訂閱方案並開啟消息讀取。

- consumer 需要定期執行其他子任務：推薦poll(較小超時時間）＋運行標識布爾變量的方式。
- consumer 不需要定期執行子任務：推薦poll(MAX_VALUE) ＋捕獲Wakeup Exception的方式。

offset 對於consumer 非常重要，因為它是實現消息交付語義保證（ message delivery semantic ）的基石。常見的3 種消息交付語義保證如下。
	- 最多一次（ at most once ）處理語義：消息可能丟失，但不會被重復處理。
	- 最少一次（ at least once ）處理語義：消息不會丟失，但可能被處理多次。
	- 精確一次（ exactly once ）處理語義：消息一定會被處理且只會被處理一次。

當消費者組首次啟動時，由於沒有初始的位移信息，coordinator必須為其確定初始位移值，這就是consumer參數auto.offset.set的作用。通常情況下，consumer要麽從最早的位移開始讀取，要麽從最新的位移開始讀取。

當consumer運行了一段時間之後，它必須要提交自己的位移值。如果consumer崩漬或被關閉，它負責的分區就會被分配給其他consumer，因此一定要在其他consumer讀取這些分區前就做好位移提交工作，否則會出現消息的重復消費。

所謂的手動位移提交就是用戶自行確定消息何時被真正處理完並可以提交位移。在一個典 型的consumer 應用場景中，用戶需要對poll 方法返回的消息集合中的消息執行業務級的處理。 用戶想要確保只有消息被真正處理完成後再提交位移。如果使用自動位移提交則無法保證這種 時序性，因此在這種情況下必須使用手動提交位移。設置使用手動提交位移非常簡單，僅僅需 要在構建Kafka Consumer 時設置enable.auto.comrnit=false ，然後調用comrnitSync 或 commitAsync 方法即可。一段典型的手動提交代碼如下：

~~~java
Propertiesprops=newProperties();
props.put("bootstrap.servers","localhost:9092");
props.put("group.id","test-group");
props.put("enable.auto.commit","false");
props.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
KafkaConsumer<String,String>consumer=newKafkaConsumer<>(props);
consumer.subscribe(Arrays.asList（"test-topic"）
final int minBatchSize=500;
List<ConsumerRecord<String,String>buffer=newArrayList<>();
while(true){
	ConsumerRecords<String,String>records=consumer.poll(lOOO);
	for(ConsumerRecord<String,String>record:records){
		buffer.add(record);
		if(buffer.size()>=minBatchSize){
			insertintoDb(buffer);
			consumer.commitSync();
			buffer.clear();
		}
	}
}
~~~

上面的代碼中consumer 持續消費一批消息並把它們加入一個緩沖區中。當積累了足夠多的消息（本例為500 條〉便統一插入到數據庫中。只有被成功插入到數據庫之後，這些消息才 算是真正被處理完。此時調用.KafkaConsumer.commitSync 方法進行手動位移提交，然後清空 緩沖區以備緩存下一批消息。若在成功插入數據庫之後但提交位移語句執行之前consumer 程 序崩潰，由於未成功提交位移， consumer 重啟後會重新處理之前的一批消息並將它們再次插入 到數據庫中，從而造成消息多次被消費。


實際使用過程中更加推薦 這個版本，因為consumer 只對它所擁有的分區做提交是更合理的行為，而且consumer 通常都 有更加細粒度化的位移提交策略。一段典型的手動提交部分分區位移的代碼如下：
~~~java
try {
    while (running) {
        ConsumerRecords < String, String > records = consumer.poll(lOOO);
        for (TopicParti tion partition: records.partitions()) {
            List < ConsumerRecord < String, String》 partitionRecords = records.records(partition);

            for (ConsumerRecord < String, String > record: partitionRecords) {
                System.out.println(record.offset() + ": "+record.value());
            }

            long lastOffset = parti tionRecords.get(partitionRecords.size() - 1).offset();
            consumer.commitSync(Collections.singletonMap(partition, new OffsetAndMetadata(lastOffset + 1)));
        }
    }
} finally {
    consumer.close();
}
~~~

上面的代碼按照分區級別進行位移提交。它首先對poll 方法返回的消息集合按照分區進行 分組。然後每個分區下的消息待處理完成後構造一個Map 對象統一提交位移，從而實現了細 粒度控制位移提交。這裏需要特別註意的是，提交的位移一定是consumer 下一條待讀取消息 的位移。這也是為什麽上面的代碼在構造OffsetMetadata 元數據時使用offset+ 1 的原因。

```








重平衡（ rebalance )
consumer group 的rebalance 本質上是一組協議，它規定了一個consumer group 是如何達成 一致來分配訂閱topic 的所有分區的。假設某個組下有20 個consumer 實例，該組訂閱了一個 有著100 個分區的topic 。正常情況下， Kafka 會為每個consumer 平均分配5 個分區。這個分 配過程就被稱為rebalance 。當consumer 成功地執行rebalance 後，組訂閱topic 的每個分區只會分配給組內的一個consumer 實例。

每個線程維護一個KafkaConsumer

```ad-note
ConsumerRunnable 類：消費線程類，執行真正的消費任務。
~~~java
public class ConsumerRunnable implements Runnable {
    //每個線程維護私有的KafkaConsumer實例
    private final KafkaConsumer<String,String> consumer;
    public ConsumerRunnable(String brokerList, String groupid, String topic) {
        Properties props = new Properties();
        props.put("bootstrap.servers", brokerList);
        props.put("group.id", groupld);
		//本例使用自動提交位移 props.put（"auto.commit.interval.ms"，"1000");
        props.put("enable.auto.comrnit", "true"）；
        props.put（"session.timeout.ms"，"3000"）；
        props.put（"key.deserializer"，"org.apache.kafka.common.serialization.StringDeserializer");
        props.put（"value.deserializer"，"org.apache.kafka.common.serialization.StringDeserializer"）；
        this.consumer = new KafkaConsumer<>(props);
		//本例使用分區副本自動分配策略
        consumer.subscribe(Arrays.asL ist(topic））；
    }

    @Override 
	public void run() {
		while(true) {
			//本例使用200 毫秒作為獲取的超時時間
            ConsumerRecords < String, String > records = consumer.poll(200);
			//這裏面寫處理消息的邏輯， 本例中只是簡單地打印消息
            for (ConsumerRecord < String, String > record: records) {
                System.out.println(Thread.current Thread().getName() + "consumed" + record.partition() + "th message with offset: "+record.offset());
            }
        }
    }
}
~~~

ConsumerGroup 類：消費線程管理類，創建多個線程類執行消費任務。

~~~java
public class ConsumerGroup {
	private List<ConsumerRunnable> consumers;
	public ConsumerGroup(int consumerNum, String groupid, String topic, String brokerList) {
		consumers = new ArrayList<>(consumerNum);
		for (int i = O; i < consumerNum; ++i) {
			ConsumerRunnable consumerThread = new ConsumerRunnable(brokerList, groupid, topic);
			consumers.add(consumerThread);
		}
	}
	public void execute() {
		for (ConsumerRunnable task: consumers) {
			new Thread(task).start();
		}
	}
}
~~~

ConsumerMain 類：測試主方法類
~~~
public class ConsumerMain {
    public static void main(String[] args) {
        String brokerList = "localhost: 9092";
        String groupid = "testGroupl";
        String topic = "test-topic";
        int consumerNum = 3;
        ConsumerGroup consumerGroup = new ConsumerGroup(consumerNum, group Id, topic, brokerList);
        consumerGroup.execute();
    }
}
~~~

```



```ad-abstract
title:單獨KafkaConsumer實例and多worker線程
將獲取的消息和消息的處理解耦，將消息的處理放入單獨的工作者線程中，即工作線程中，同時維護一個或者若各幹consumer實例執行消息獲取任務。  
本例使用全局的KafkaConsumer實例執行消息獲取，然後把獲取到的消息集合交給線程池中的worker線程執行工作，之後worker線程完成處理後上報位移狀態，由全局consumer提交位移。

~~~java
/**
 *
 * @param <K>
 * @param <V>
 *
 * consumer多線程管理類，用於創建線程池以及為每個線程分配消息集合。 另外consumer位移提交也在該類中完成。
 *
 */
public class ConsumerThreadHandler<K, V> {

    // KafkaConsumer實例
    private final KafkaConsumer<K, V> consumer;
    // ExecutorService實例
    private ExecutorService executors;
    // 位移信息offsets
    private final Map<TopicPartition, OffsetAndMetadata> offsets = new HashMap<>();

    /**
     *
     * @param brokerList
     *            kafka列表
     * @param groupId
     *            消費組groupId
     * @param topic
     *            主題topic
     */
    public ConsumerThreadHandler(String brokerList, String groupId, String topic) {
        Properties props = new Properties();
        // broker列表
        props.put("bootstrap.servers", brokerList);
        // 消費者組編號Id
        props.put("group.id", groupId);
        // 非自動提交位移信息
        props.put("enable.auto.commit", "false");
        // 從最早的位移處開始消費消息
        props.put("auto.offset.reset", "earliest");
        // key反序列化
        props.put("key.deserializer", "org.apache.kafka.common.serialization.ByteArrayDeserializer");
        // value反序列化
        props.put("value.deserializer", "org.apache.kafka.common.serialization.ByteArrayDeserializer");
        // 將配置信息裝配到消費者實例裡面
        consumer = new KafkaConsumer<>(props);
        // 消費者訂閱消息，並實現重平衡rebalance
        // rebalance監聽器，創建一個匿名內部類。使用rebalance監聽器前提是使用消費者組（consumer group）。
        // 監聽器最常見用法就是手動提交位移到第三方存儲以及在rebalance前後執行一些必要的審計操作。
        consumer.subscribe(Arrays.asList(topic), new ConsumerRebalanceListener() {

            /**
             * 在coordinator開啟新一輪rebalance前onPartitionsRevoked方法會被調用。
             */
            @Override
            public void onPartitionsRevoked(Collection<TopicPartition> partitions) {
                // 提交位移
                consumer.commitSync(offsets);
            }

            /**
             * rebalance完成後會調用onPartitionsAssigned方法。
             */
            @Override
            public void onPartitionsAssigned(Collection<TopicPartition> partitions) {
                // 清除位移信息
                offsets.clear();
            }
        });
    }

    /**
     * 消費主方法
     *
     * @param threadNumber
     *            線程池中的線程數
     */
    public void consume(int threadNumber) {
        executors = new ThreadPoolExecutor(
                threadNumber,
                threadNumber,
                0L,
                TimeUnit.MILLISECONDS,
                new ArrayBlockingQueue<Runnable>(1000),
                new ThreadPoolExecutor.CallerRunsPolicy());
        try {
            // 消費者一直處於等待狀態，等待消息消費
            while (true) {
                // 從主題中獲取消息
                ConsumerRecords<K, V> records = consumer.poll(Duration.ofSeconds(1000L));
                // 如果獲取到的消息不為空
                if (!records.isEmpty()) {
                    // 將消息信息、位移信息封裝到ConsumerWorker中進行提交
                    executors.submit(new ConsumerWorker<>(records, offsets));
                }
                // 調用提交位移信息、儘量降低synchronized塊對offsets鎖定的時間
                this.commitOffsets();
            }
        } catch (WakeupException e) {
            // 此處忽略此異常的處理.WakeupException異常是從poll方法中拋出來的異常
            //如果不忽略異常信息，此處會列印錯誤哦，親
            //e.printStackTrace();
        } finally {
            // 調用提交位移信息、儘量降低synchronized塊對offsets鎖定的時間
            this.commitOffsets();
            // 關閉consumer
            consumer.close();
        }
    }

    /**
     * 儘量降低synchronized塊對offsets鎖定的時間
     */
    private void commitOffsets() {
        // 儘量降低synchronized塊對offsets鎖定的時間
        Map<TopicPartition, OffsetAndMetadata> unmodfiedMap;
        // 保證線程安全、同步鎖，鎖住offsets
        synchronized (offsets) {
            // 判斷如果offsets位移信息為空，直接返回，節省同步鎖對offsets的鎖定的時間
            if (offsets.isEmpty()) {
                return;
            }
            // 如果offsets位移信息不為空，將位移信息offsets放到集合中，方便同步
            unmodfiedMap = Collections.unmodifiableMap(new HashMap<>(offsets));
            // 清除位移信息offsets
            offsets.clear();
        }
        // 將封裝好的位移信息unmodfiedMap集合進行同步提交
        // 手動提交位移信息
        consumer.commitSync(unmodfiedMap);
    }

    /**
     * 關閉消費者
     */
    public void close() {
        // 在另一個線程中調用consumer.wakeup();方法來觸發consume的關閉。
        // KafkaConsumer不是線程安全的，但是另外一個例外，用戶可以安全的在另一個線程中調用consume.wakeup()。
        // wakeup()方法是特例，其他KafkaConsumer方法都不能同時在多線程中使用
        consumer.wakeup();
        // 關閉ExecutorService實例
        executors.shutdown();
    }

}
~~~

~~~java
/**
 *
 * @param <K>
 * @param <V>
 *
 * 本質上是一個Runnable，執行真正的消費邏輯並且上報位移信息給ConsumerThreadHandler。
 *
 */
public class ConsumerWorker<K, V> implements Runnable {

    // 獲取到的消息
    private final ConsumerRecords<K, V> records;
    // 位移信息
    private final Map<TopicPartition, OffsetAndMetadata> offsets;

    /**
     * ConsumerWorker有參構造方法
     *
     * @param records
     *            獲取到的消息
     * @param offsets
     *            位移信息
     */
    public ConsumerWorker(ConsumerRecords<K, V> records, Map<TopicPartition, OffsetAndMetadata> offsets) {
        this.records = records;
        this.offsets = offsets;
    }

    /**
     *
     */
    @Override
    public void run() {
        // 獲取到分區的信息
        for (TopicPartition partition : records.partitions()) {
            // 獲取到分區的消息記錄
            List<ConsumerRecord<K, V>> partConsumerRecords = records.records(partition);
            // 遍歷獲取到的消息記錄
            for (ConsumerRecord<K, V> record : partConsumerRecords) {
                // 列印消息
                System.out.println("topic: " + record.topic() + ",partition: " + record.partition() + ",offset: "
                        + record.offset()
                        + ",消息記錄: " + record.value());
            }
            // 上報位移信息。獲取到最後的位移消息，由於位移消息從0開始，所以最後位移減一獲取到位移位置
            long lastOffset = partConsumerRecords.get(partConsumerRecords.size() - 1).offset();
            // 同步鎖，鎖住offsets位移
            synchronized (offsets) {
                // 如果offsets位移不包含partition這個key信息
                if (!offsets.containsKey(partition)) {
                    // 就將位移信息設置到map集合裡面
                    offsets.put(partition, new OffsetAndMetadata(lastOffset + 1));
                } else {
                    // 否則，offsets位移包含partition這個key信息
                    // 獲取到offsets的位置信息
                    long curr = offsets.get(partition).offset();
                    // 如果獲取到的位置信息小於等於上一次位移信息大小
                    if (curr <= lastOffset + 1) {
                        // 將這個partition的位置信息設置到map集合中。並保存到broker中。
                        offsets.put(partition, new OffsetAndMetadata(lastOffset + 1));
                    }
                }
            }
        }
    }
}
~~~


~~~java
/**
 *
 * 1、單獨KafkaConsumer實例和多worker線程。
 * 2、將獲取的消息和消息的處理解耦，將消息的處理放入單獨的工作者線程中，即工作線程中，同時維護一個或者若各幹consumer實例執行消息獲取任務。
 * 3、本例使用全局的KafkaConsumer實例執行消息獲取，然後把獲取到的消息集合交給線程池中的worker線程執行工作，之後worker線程完成處理後上報
 * 位移狀態，由全局consumer提交位移。
 *
 *
 */

public class ConsumerMain {

    public static void main(String[] args) {
        // broker列表
        String brokerList = "slaver1:9092,slaver2:9092,slaver3:9092";
        // 主題信息topic
        String topic = "topic1";
        // 消費者組信息group
        String groupId = "group2";
        // 根據ConsumerThreadHandler構造方法構造出消費者
        final ConsumerThreadHandler<byte[], byte[]> handler = new ConsumerThreadHandler<>(brokerList, groupId, topic);
        final int cpuCount = Runtime.getRuntime().availableProcessors();
        System.out.println("cpuCount : " + cpuCount);
        // 創建線程的匿名內部類
        Runnable runnable = new Runnable() {

            @Override
            public void run() {
                // 執行consume，在此線程中執行消費者消費消息。
                handler.consume(cpuCount);
            }
        };
        // 直接調用runnable此線程，並運行
        new Thread(runnable).start();

        try {
            // 此線程休眠20000
            Thread.sleep(20000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Starting to close the consumer...");
        // 關閉消費者
        handler.close();
    }

}
~~~


```

---

```ad-note
title: 異步發送
collapse: open
~~~java
producer.send(record, new Callback() {
	@Override
	public void onCornpletion(RecordMetadata rnetadata, Exception exception){
		if (exception == null) {
		／／消息發送成功
		} else {
		／／執行錯誤處理邏輯
		}
	}
});
~~~
```

```ad-note
title: 同步發送
collapse: open
~~~java
ProducerRecord<String,String> record= new ProducerRecord<>("test", Integer.toString(i));
producer.send(record).get();
~~~
```
- 當producer 發送一條消息給Kafka 集群時，這條消息會被發送到指定topic 分區leader 所在的broker 上， producer 等待從該leader broker 返回消息的寫入結果（當然並不是無限等待，是有超時時間的）以確定消息被成功提交。
- producer 端越快地接收到leader broker 響應，它就能越快地發送下一條消息，即吞吐量也就越大。producer 端的acks 參數就是用來控制做這件事情的。
```ad-note
title: acks 參數
collapse: open
1. acks = 0 ：設置成0 表示producer 完全不理睬leader broker 端的處理結果。通常這種設置下producer 的吞吐量是最高的
2. acks = all 或者－ 1 ：表示當發送消息時， leader broker 不僅會將消息寫入本地日志，同時還會等待ISR 中所有其他副本都成功寫入它們各自的本地日志後，才發送響應結果給producer 。
3. 以達到最高的消息持久性，但通常這種設置下producer 的吞吐量也是最低的。那麽此時只要該leader broker 一直存活， Kafka 就能夠保證這條消息不丟失
```

```ad-note
title: acks配置
collapse: open
~~~java
props.put ("acks ","1");
//or
props.put(ProducerConfig.ACKS_CONFIG ，"1"）；
~~~
```

```ad-note
title: buffer.memory (default 32M)
collapse: open
~~~java
props.put ("buffer.memory", 33554432);
//or
props.put(ProducerConfig.BUFFER_MEMORY_CONFIG , 33554432)
~~~
```

```ad-note
title: compression. type (GZIP 、Snappy 和LZ4)
collapse: open
~~~java
props.put("compression.type","lz4");
//or
props.put(ProducerConfig.COMPRESSION_TYPE_CONFIG ,"lz4");
~~~
```

```ad-note
title: retries
collapse: open
~~~java
props.put("retries", 100);
//or
props.put(ProducerConfig.RETRIES_CONFIG, 100);
~~~
```

```ad-note
title: batch.size
collapse: open
batch.size 参数默认值是16384 ，即16KB，在實際使用過程中合理地增加該參數值，通常都會發現producer 的吞吐量得到了相應的增加。
~~~java
props.put("batch.size", 1048576) ;
//or
props.put(ProducerConfig.BATCH_SIZE_CONFIG, 1048576 );
~~~
```

```ad-note
title: linger.ms 
collapse: open
參數就是控制消息發送延時行為的，該參數默認值是0 ，表示消息需要被立即發送
~~~java
props.put("linger.ms ", 100) ;
//or
props.put(ProducerConfig.LINGER_MS_CONFIG , 100) ;
~~~
```

 ```ad-note
title: max.request.size 
collapse: open
實際上該參數控制的是producer 端能夠發送的最大消息大小
```

 ```ad-note
title: request.timeout.ms
collapse: open
當producer 發送請求給broker 後， broker 需要在規定的時間範圍內將處理結果返還給producer 。這段時間便是由該參數控制的，默認是30 秒。
```

 ```ad-note
title:  partition 方法，該方法接收消息所屬的topic 、key 和value,還有集群的元數據信息， 一起來確定目標分區
collapse: open
~~~java
public class AuditPartitioner implements Partitioner { ... }
//props.put("partitioner.class" ,"com.xxx.yyy.producer.AuditPartitioner");
props.put(ProducerConfig.PARTITIONER_CLASS_CONFIG,"com.xxx.yyy.producer.AuditPartitioner");
~~~
```

 ```ad-note
title:  interceptor - Producer
collapse: open
~~~java
public class TimeStampPrependerinterceptor implements Producerinterceptor<String, String> {
	props.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG, interceptors);
	Producer<String, String> producer= new KafkaProducer<>(props);
	//一定要關閉producer ，這樣才會調用interceptor 的close 方法
	producer.close();
}
~~~
```

```ad-note
title:  KafkaListener定時啟動

`@KafkaListenerEndpointRegistry`

```