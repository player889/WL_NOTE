@ConditionalOnProperty
Check if the define value matchs the value in property file

```java
@ConditionalOnProperty(prefix = "optimus", name = "ias.datasync.enable-temporal", havingValue = "true", matchIfMissing = false)
@Service
public class DataSyncOutboxService {
```

How to create a custom annotation in Spring Boot?
https://fullstackdeveloper.guru/2021/06/15/how-to-create-a-custom-annotation-in-spring-boot/

---

實體類上加入@EntityListeners(AuditingEntityListener.class)，啟動類上加入註解@EnableJpaAuditing，這樣就實現了類似公共字段自動填充功能了@CreatedDate、@LastModifiedDate、@CreatedBy、@LastModifiedBy。
https://www.cnblogs.com/niceyoo/p/10908647.html
https://hackmd.io/@winnienotes/ry-62tqZ9
![[Pasted image 20230807094425.png|350]]

```java
@EventListener(ApplicationReadyEvent.class)
```

Get the value from specific property file.

```
@Data
@Configuration
@PropertySource("classpath:debezium.properties")
@ConfigurationProperties(prefix = "debezium.oracle")
```
