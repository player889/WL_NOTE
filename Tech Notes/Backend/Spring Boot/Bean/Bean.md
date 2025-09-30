```java
@Controller
@RequestMapping("/")
@Scope("prototype")
public class TestController {
    private static int a = 0; //静态
    private int b = 0;     //非静态

    @RequestMapping("/test")
    public void test() {
        a++;
        b++;
        System.out.println(a + " | " + b);
    }
```

列印結果：

單例 (singleton)

0 | 0

1 | 1

2 | 2

3 | 3

4 | 4

多例 (prototype)

0 | 0

1 | 0

2 | 0

3 | 0

4 | 0

通過以上結果再次證明了 singleton 只有一個實例；prototype 每次重新請求都會 new 一個新的實例

# 牢記這 16 個 SpringBoot 擴展介面，寫出更加漂亮的程式碼
https://juejin.cn/post/7271924341734228023
