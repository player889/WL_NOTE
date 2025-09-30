### @Async 實現原理

- 在 SpringBoot 中，註解意味著 AOP，而 AOP 意味著代理類。所以@Async 的實現原理就是基於代理類實現的
- 執行執行緒是如何建立的？
    - 先根據類型從容器中尋找類型為 TaskExecutor 的類，即執行緒池組態
    - 再根據名稱從容器中尋找名稱為 taskExecutor 的類，還是再找執行緒池組態
    - 如果確實沒有定義執行緒池，那麼將實例化一個 Thread 對象來執行。

### @Async 註解會在以下幾個場景失效

- 非同步方法使用 static 關鍵詞修飾;
- 非同步類不是一個 Spring 容器的 bean；
- SpringBoot 應用中沒有新增@EnableAsync 註解；
- 在同一個類中，一個方法呼叫另外一個有@Async 註解的方法，註解不會生效。原因是@Async 註解的方法，是在代理類中執行的。

### 返回值

需要注意的是： 非同步方法使用註解@Async 的返回值只能為 void 或者 Future 及其子類，當返回結果為其他類型時，方法還是會非同步執行，但是返回值都是 null

```java
@RestControllerpublic class AsyncController {

    @Autowired
    private AsyncService asyncService;

    @SneakyThrows
    @ApiOperation("异步 有返回值")
    @GetMapping("/open/somethings")
    public String somethings() {

        CompletableFuture<String> createOrder = asyncService.doSomething1("create order");
        CompletableFuture<String> reduceAccount = asyncService.doSomething2("reduce account");
        CompletableFuture<String> saveLog = asyncService.doSomething3("save log");

        // 等待所有任务都执行完
        CompletableFuture.allOf(createOrder, reduceAccount, saveLog).join();
        // 获取每个任务的返回结果
        String result = createOrder.get() + reduceAccount.get() + saveLog.get();
        return result;
    }}

@Slf4j
@Service
public class AsyncService {

    @Async("doSomethingExecutor")
    public CompletableFuture<String> doSomething1(String message) throws InterruptedException {
        log.info("do something1: {}", message);
        Thread.sleep(1000);
        return CompletableFuture.completedFuture("do something1: " + message);
    }

    @Async("doSomethingExecutor")
    public CompletableFuture<String> doSomething2(String message) throws InterruptedException {
        log.info("do something2: {}", message);
        Thread.sleep(1000);
        return CompletableFuture.completedFuture("; do something2: " + message);
    }

    @Async("doSomethingExecutor")
    public CompletableFuture<String> doSomething3(String message) throws InterruptedException {
        log.info("do something3: {}", message);
        Thread.sleep(1000);
        return CompletableFuture.completedFuture("; do something3: " + message);
    }

}
```

```ad-note
title: More CompletableFuture Example
~~~java
@Service
@Slf4j
class Service1 {
    @Async
    public CompletableFuture<Integer> doSomething() throws InterruptedException {
        Thread.sleep(2000);
        log.info("在Service1中");
        return new AsyncResult<Integer>(1).completable();
    }
}
~~~


~~~java
@Service
@Slf4j
class Service2 {
    @Async
    public CompletableFuture<Integer> doSomething() throws InterruptedException {
        Thread.sleep(2000);
        log.info("在Service2中");
        return new AsyncResult<Integer>(2).completable();
    }
}
~~~


~~~java
@Bean
CommandLineRunner commandLineRunner(Service1 service1, Service2 service2){
   return p -> {
      long start = System.currentTimeMillis();
      CompletableFuture<Integer> firstData = service1.doSomething();
      CompletableFuture<Integer> secondData = service2.doSomething();
      CompletableFuture<Integer> mergeResult = firstData.thenCompose(
                                          firstValue -> secondData.thenApply(
                                                secondValue -> firstValue + secondValue
                                          )
                                    );
      long end = System.currentTimeMillis();
      Long cost = end - start;
      log.info("结果为:" + mergeResult.get() + ",耗时" + cost);
   };
}

~~~

thenApply（）轉換的是泛型中的類型，是同一個CompletableFuture；
thenCompose（）用來連接兩個CompletableFuture，是生成一個新的CompletableFuture。他們都是讓CompletableFuture可以對返回的結果進行後續操作，就像流一樣進行map和flatMap的裝換。

```

````

## 使用 Guava 的 MoreExecutors

MoreExecutors 是一個多執行緒執行器的工具類，用來生成各種 Executors 類。比如生成一個可以隨著 jvm 關閉而關閉的執行緒池、建立一個順序執行的 Executor 等等。

通過向 listeningDecorator 裝飾器中傳遞 ThreadPoolExecutor 就可以初始化一個 ListeningExecutorService ，ListeningExecutorService 可以為執行緒新增回呼函數

如下程式碼演示了 ListeningExecutorService 的基本使用方法：

```java
@Resource(name = "threadPoolExecutor")private ThreadPoolExecutor threadPoolExecutor;

public String case4() {
    ListeningExecutorService executorService = MoreExecutors.listeningDecorator(threadPoolExecutor);
    for (int i = 0; i < 5; i++) {
        ListenableFuture<String> submit = executorService.submit(new Callable<String>() {
            @Override
            public String call() throws Exception {
                Thread.sleep( 1000);
                return "ok";
            }
        });
        submit.addListener(new Runnable() {
            @Override
            public void run() {
                log.info("執行緒回呼：" + Thread.currentThread().getName());
            }
        }, executorService);

    }
    return null;
}
````

### 聊聊 CountDownLatch

CountDownLatch 可以理解為一個工具，借助此工具可以實現主執行緒等待所有子執行緒執行完畢後在做其他操作的功能。

下面示例程式碼演示了使用方法：

```java
// 初始化CountDownLatch
final CountDownLatch latch = new CountDownLatch(projectIds.size());
projectIds.forEach(it -> {
    threadTaskExecutor.execute(() -> {
        try {
            // 業務程式碼
        } catch (Exception e) {
            // 異常資訊捕獲
        } finally {
            // 將計數器減1
            latch.countDown();
        }
    });
});
// 等待計算器的值變為0，此處會發生堵塞
latch.await();
```

**CountDownLatch 有什麼用？**

CountDownLatch 允許 count 個執行緒阻塞在一個地方，直至所有執行緒的任務都執行完畢。CountDownLatch 是一次性的，計數器的值只能在構造方法中初始化一次，之後沒有任何機制再次對其設定值，當 CountDownLatch 使用完畢後，它不能再次被使用。

在這個項目中，我們要讀取處理 3 個檔案，這 3 個任務都是沒有執行順序依賴的任務，但是我們需要返回給使用者的時候將這幾個檔案的處理的結果進行統計整理。為此我們定義了一個執行緒池和 count 為 3 的 CountDownLatch 對象 。使用執行緒池處理讀取任務，每一個執行緒處理完之後就將 count-1，呼叫 CountDownLatch 對象的 await()方法，直到所有檔案讀取完之後，才會接著執行後面的邏輯。

![[Pasted image 20230815101440.png|600]]

** CyclicBarrier**

CyclicBarrier 和 CountDownLatch 非常類似，它也可以實現執行緒間的技術等待，但是它的功能比 CountDownLatch 更加複雜和強大。主要應用場景和 CountDownLatch 類似。CountDownLatch 的實現是基於 AQS 的，而 CycliBarrier 是基於 ReentrantLock(ReentrantLock 也屬於 AQS 同步器)和 Condition 的。CyclicBarrier 的字面意思是可循環使用（Cyclic）的屏障（Barrier）。它要做的事情是：讓一組執行緒到達一個屏障（也可以叫同步點）時被阻塞，直到最後一個執行緒到達屏障時，屏障才會開門，所有被屏障攔截的執行緒才會繼續幹活。
