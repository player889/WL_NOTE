https://juejin.cn/post/7055646857241821215
[Ref4](https://z.itpub.net/article/detail/61C68F03A99045B16039AFF665F92A25)  
[Ref5](https://it-blog-cn.com/blogs/parallel/completablefuture.html)  
[Reference](https://www.bilibili.com/video/BV1V8411U7s7/?spm_id_from=333.1007.tianma.12-1-43.click&vd_source=7dbbe05f9c47ef882f5a621f94e1d693)  
[Ref2](https://z.itpub.net/article/detail/61C68F03A99045B16039AFF665F92A25)  
[Ref3](https://www.lwohvye.com/2021/09/18/completablefuture%E5%90%84%E7%A7%8D%E7%94%A8%E6%B3%95%EF%BC%88%E8%BD%AC%EF%BC%89/)  
使用自定義線呈池  
![[Pasted image 20230816101008.png]]

- \*`runAysnc()` 沒有返回值，使用`SupplyAysnc()`
- if CompletableFuture using `*Async` method, it is possible need to use `join()`

![[Pasted image 20230816174449.png]]

![[Pasted image 20230816174643.png]]

## ==then==

![[Pasted image 20230816101757.png]]
![[Pasted image 20230816102226.png]]![[Pasted image 20230816102437.png]]
![[Pasted image 20230816102550.png]]

##### all

> [!important] CompletableFuture.allOf is for ==void== CompletableFuture method.

![[Pasted image 20230816102742.png]]
![[Pasted image 20230816102804.png]]

### getNow - return default value

![[Pasted image 20230816103315.png]]
![[Pasted image 20230816103002.png]]

![[Pasted image 20230816103405.png]]
![[Pasted image 20230816103545.png]]

##### complete 超時處理

![[Pasted image 20230816103814.png]]

```java
/**
 - 假设有个需求：获取位置坐标有两种方式，百度地图/谷歌地图
 - <p>
 - 要求：同时调用百度/谷歌地图的接口，哪个先返回就用哪个的结果作为位置坐标，这样性能最佳。
 *
 - @param args
 */
public static void main(String[] args) {
    System.out.println(CompletableFuture.supplyAsync(() -> {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return 100;
    }).applyToEither(CompletableFuture.supplyAsync(() -> {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return 200;
    }), f -> {
        return f;
    }).join());
}

```

##### Return in 5 sec or call another method

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;

public class CompletableFutureTimeoutExample {

    public static void main(String[] args) {
        // Simulating a REST API call that takes longer than 5 seconds to respond
        CompletableFuture<String> apiResponseFuture = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(10); // Simulate a slow API call
                return "API response data";
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        });

        // Define a fallback method to be executed if the API response is not received within 5 seconds
        CompletableFuture<String> fallbackFuture = CompletableFuture.supplyAsync(() -> {
            // Perform some alternative action when API response is delayed
            return "Fallback method executed";
        });

        // Combine the API response CompletableFuture with a timeout
        CompletableFuture<String> resultFuture = apiResponseFuture
                .completeOnTimeout("API response timed out", 5, TimeUnit.SECONDS)
                .applyToEither(fallbackFuture, apiResponse -> apiResponse);

        try {
            // Get the result from the CompletableFuture, waiting for either API response or fallback
            String result = resultFuture.get();
            System.out.println("Result: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}

```
