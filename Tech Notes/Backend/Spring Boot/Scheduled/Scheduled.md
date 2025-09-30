在使用@Scheduled 註解時，我們很容易忽略一個問題：如果定時任務在執行時，下一個週期的任務已經到了，那麼後續任務可能會受到影響。例如，我們定義了一個間隔時間為 5 秒的定時任務 A，在第 1 秒時開始執行，需要執行 10 秒鐘。在第 6 秒時，定時任務 A 還沒有結束，此時下一個週期的任務 B 已經開始等待執行。如果此時執行緒池中沒有足夠的空閒執行緒，那麼定時任務 B 就會被阻塞，無法執行。

為了避免上述問題，可以將@Scheduled 任務交給執行緒池進行處理。在 Spring Boot 中，可以通過以下兩種方式來將@Scheduled 任務加入執行緒池：

##### 使用@EnableScheduling + @Configuration 組態 ThreadPoolTaskScheduler

```java
@Configuration
@EnableScheduling
public class TaskSchedulerConfig {
	@Bean
	public TaskScheduler taskScheduler() {
	    ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
	    scheduler.setPoolSize(10);
	    scheduler.initialize();
	    return scheduler;
	}
}
```

##### 我們通過組態 ThreadPoolTaskScheduler 來建立一個執行緒池，並使用@EnableScheduling 註解將定時任務開啟。其中，setPoolSize 方法可以設定執行緒池的大小

```java
@Configuration
@EnableScheduling
public class TaskExecutorConfig {
	@Bean
	public ThreadPoolTaskExecutor taskExecutor() {
	    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
	    executor.setCorePoolSize(10);
	    executor.setMaxPoolSize(50);
	    executor.setQueueCapacity(1000);
	    executor.setKeepAliveSeconds(60);
	    executor.setThreadNamePrefix("task-executor-");
	    return executor;
	}
}
```

在 Spring Boot 中，@Scheduled 註解是基於 Java 的 ThreadPoolExecutor 和 ScheduledThreadPoolExecutor 實現的。當我們組態了一個定時任務後，Spring Boot 會首先建立一個 ScheduledThreadPoolExecutor 執行緒池，並將定時任務新增到該執行緒池中等待執行。然後，在指定的時間到來之後，執行緒池會為該定時任務分配一個執行緒來執行。如果該定時任務還未執行完畢，在下一個週期到達時，執行緒池會為該任務再次分配一個執行緒來執行。通過這種方式，@Scheduled 可以非常方便地實現週期性的定時任務。

---

雖然@Scheduled 註解非常便捷，但是它也存在一些多執行緒的問題，主要體現在以下兩個方面：

```ad-note
title: 定時任務未執行完畢時，後續任務可能會受到影響

在使用@Scheduled 註解時，我們很容易忽略一個問題：如果定時任務在執行時，下一個週期的任務已經到了，那麼後續任務可能會受到影響。例如，我們定義了一個間隔時間為 5 秒的定時任務 A，在第 1 秒時開始執行，需要執行 10 秒鐘。在第 6 秒時，定時任務 A 還沒有結束，此時下一個週期的任務 B 已經開始等待執行。如果此時執行緒池中沒有足夠的空閒執行緒，那麼定時任務 B 就會被阻塞，無法執行。

> 我們可以對執行緒池進行組態，提高執行緒池的大小，從而確保有足夠的空閒執行緒來處理定時任務。
> 例如，我們可以在 application.properties 或 application.yml 或者使用@EnableScheduling + @Configuration
> 組態執行緒池大小： spring.task.scheduling.pool.size=20

```

---

```ad-note
title: 多個定時任務並行執行可能導致資源競爭
在某些情況下，我們可能需要編寫多個定時任務，這些定時任務可能涉及到共享資源，例如資料庫連接、快取對象等。當多個定時任務同時執行時，就會存在資源競爭的問題，可能會導致資料錯誤或者係統崩潰。
解決方案：
==**使用鎖機制**== - 鎖機制是一種常見的解決多執行緒並行訪問共享資源的方式。在 Java 中，我們可以使用 synchronized 關鍵字或者 Lock 介面來實現鎖機制。例如，下面是一個使用 synchronized 關鍵字實現鎖機制的示例：
~~~java
private static Object lockObj = new Object();

@Scheduled(fixedDelay = 1000)
public void doSomething(){
	synchronized(lockObj){
    // 定時任務要執行的內容
	}
}
~~~
在上述程式碼中，我們定義了一個靜態對象lockObj，用來保護共享資源。在定時任務執行時，我們使用synchronized關鍵字對lockObj進行加鎖，從而確保多個定時任務不能同時訪問共享資源。

==**使用分佈式鎖**== - 除了使用傳統的鎖機制外，還可以使用分佈式鎖來解決資源競爭問題。分佈式鎖是一種基於分佈式系統的鎖機制，它可以不依賴於單個JVM實例，從而能夠保證多個定時任務之間的資源訪問不會衝突。
在Java開發中，我們可以使用ZooKeeper、Redis等分佈式系統來實現分佈式鎖機制。


```
