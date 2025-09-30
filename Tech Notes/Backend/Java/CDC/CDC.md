# CDC - Change Database Capture

![[Pasted image 20230421151755.png|600]]

* # Debezium
* outbox pattern
* strangler Fig Pattern
* As mentioned earlier , Apache Kafdrop is an UI tool to monitor Kafka messages.
* # Kafka Connect JDBC Connector学习文档

---
```java
@Service
public class DatabaseChangeService {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void notifyDatabaseChange(String tableName, String operation) {
        String message = tableName + " " + operation;
        kafkaTemplate.send("database-changes", message);
    }

}
```
```java
@Service
public class DatabaseChangeConsumer {

    @KafkaListener(topics = "database-changes", groupId = "ias-consumer")
    public void processDatabaseChange(String message) {
        String[] parts = message.split(" ");
        String tableName = parts[0];
        String operation = parts[1];
        // process the database change here
    }

}
```

```properties
spring.kafka.producer.bootstrap-servers=localhost:9092
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
```

```properties
spring.kafka.consumer.bootstrap-servers=localhost:9092
spring.kafka.consumer.group-id=ias-consumer
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
```

Debezium config with mutiple tables
```json
{
  "name": "my-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "database.hostname": "localhost",
    "database.port": "3306",
    "database.user": "debezium",
    "database.password": "dbz",
    "database.server.id": "1",
    "database.server.name": "my-app-db",
    "table.include.list": "mydb.customers,mydb.orders"
  }
}
```
Debezium config with specifc tables

```json
{
  "name": "my-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "database.hostname": "localhost",
    "database.port": "3306",
    "database.user": "debezium",
    "database.password": "dbz",
    "database.server.id": "1",
    "database.server.name": "my-app-db",
    "table.include.list": "mydb.customers",
    "column.include.list": "mydb.customers.email,mydb.customers.phone"
  }
}
```

Debezium also provides a Kafka Connect REST API, which you can use to manage the connectors, such as creating or updating a connector configuration. You can use Spring's `RestTemplate` to interact with the Kafka Connect REST API in your Spring Boot application.

Dynamic change the properties

```http
PUT /connectors/my-connector/config HTTP/1.1 
Content-Type: application/json 
{ "table.include.list": "mydatabase.mytable,mydatabase.myothertable" }
```
To send a `PUT` request to the connector endpoint, you can use Spring's `RestTemplate` or any other HTTP client library in your Spring Boot application.

Overall, Debezium's REST API provides a convenient way to update the configuration of a connector at runtime, allowing you to dynamically change the list of tables that the connector captures changes for without restarting the connector.



*OLTP（Online Transaction Processing)*