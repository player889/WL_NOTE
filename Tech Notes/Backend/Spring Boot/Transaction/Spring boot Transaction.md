# Propagation behavior

## Propagation.REQUIRED

```java
@Transactional
public void service(){
    serviceA();
    serviceB();
}

@Transactional
serviceA();
@Transactional
serviceB();
```

1. 如果它作為一個子事務方法, 在其他事務方法中被呼叫, 那麼該方法不會建立新的事務, 使用現有的==父等級==的事務.
2. 如果它作為一個子事務方法, 沒有在其他事務方法中被呼叫, 而是在==非事務方法==中直接呼叫, 那麼它會建立一個新的事務來執行資料庫操作.

**應用場景**: 不知道方法的呼叫者是否建立了事務, 但是要求當前被呼叫的方法必須在一個事務當中執行.

## Propagation.MANDATORY

兩個帳戶  **A**  和  **B**  之間轉賬, 從  **A**  轉  **1000**  到  **B** , 拆分為兩個操作, 並且要在一個事務中執行.

操作 1: 先把  **1000**  加到  **B**  帳戶上, 如果  **B**  帳戶增加成功, 執行操作 2  
操作 2: 如果  **B**  帳戶增加成功, 那麼  **A**  帳戶扣除  **1000**.

當兩個操作同時成功時, 事務執行成功, 否則, 任意一步錯誤, 回滾之前的全部操作. 那麼對應的操作 1 和操作 2 我們可以實現為兩個  `Propagation.MANDATORY`  類型的 Java 方法. 並且把這兩個方法放在一個  `Propagation.REQUIRED`  類型的方法中, 例如:

```java
// 服务接口

interface AccountService {
    TransactionLog transfer(Long accountA, Long accountB, BigDecimal amount);
}

// 服务实现

@Service
class AccountServiceImpl implements AccountService {
    // 父事务, 用于组织多个子事务.
    @Transactional(propagation = Propagation.REQUIRED, rollbackFor = Exception.class)
    public TransactionLog transfer(Long accountA, Long accountB, BigDecimal amount) {
        // 先加后减
        addAmount(accountB, amount)
        addAmount(accountA, amount.negate())
    }
}

// 数据库访问对象

@Repository
class interface AccountRepository extends JpaRepository<Account, Long> {
        // 余额操作(自增, 自减)
    @Modifying
    @Transactional(propagation = Propagation.MANDATORY)
    @Query(value = "UPDATE account a SET a.balance = a.balance + ?2 WHERE a.id = ?1")
    void addAmount(Long id, BigInteger amount);
}
```

## Propagation.REQUIRES_NEW

> **新建事務,如果當前存在事務,把當前事務掛起**  
> 特徵: 啟動一個新的, 不依賴於環境的 "內部" 事務. 這個事務將被完全  **提交**  或  **回滾**  而不依賴於外部事務,它擁有自己的隔離範圍, 自己的鎖, 等等.當內部事務開始執行時, 外部事務將被掛起, 內務事務結束時, 外部事務將繼續執行. **Propagation.REQUIRES_NEW**  常用於日誌記錄,或者交易失敗仍需要留痕.
>
> 還有就是  **時序控制**, 支付依賴於已經建立的訂單, 無訂單不支付. 先要有訂單才能支付. 常見於事務步驟  **要求時序**  的情況.

開啟一個全新的事務, 一般用於分佈式事務中的一個前期步驟. 比如一鍵購功能. 主要步驟包括:

- **建立訂單**
    - 建立訂單
    - 扣除庫存
    - 建立物流訂單
- **發起支付**

```java
// 一鍵購服務
class BuyService {
    @Transactional(propagation = Propagation.REQUIRED)
    public void buyDirectly() {
        Order OrderService.createOrder(OrderDto orderDto);
        PayService.pay(PayDto payDto);
    }
}
interface OrderService {
    void createOrder(OrderDto orderDto);
}
interface PayService {
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    Payment pay(PayDto payDto);
}
```

**OrderService.createOrder**  運行在外層事務中, 如果建立訂單失敗, 就沒必要發起支付了, 直接回滾了, 根本就到不了支付這一步.

當繫結的銀行卡餘額不足的情況, **PayService.pay();**  是可以回滾的, 而不會影響  **buyDirectly**  整個事務, **OrderService.createOrder();**  成功. 向銀行卡不足金額後, 可以重新發起支付, 完成購買過程.

> 注意: **Propagation.REQUIRES_NEW**  如果作為一個子事務運行, 呼叫者和被調這不要在同一個服務類中(因為 Spring AOP 動態代理的限制, 在同一個類中事務是不起作用的)
>
> **Propagation.REQUIRES_NEW**  的一般使用場景是作為內層事務可以單獨回滾. 而不是回滾整個外層事務. 因此如果呼叫者和被呼叫者如果在一個類中, **Propagation.REQUIRES_NEW**  註解的方法並  **不會**  開啟一個新的事務. 因此就達不到內層事務單獨回滾的目的.
>
> **歸納: 內層事務可以獨立回滾, 不影響外層事務.**  
> 前提是外層事務的方法不能和內層事務的方法在同一個服務類中

## Propagation.NESTED

**巢狀事務, 它的作用相當於 Propagation.REQUIRED 和 Propagation.REQUIRES_NEW 的合體**

特徵: **Propagation.NESTED**  啟動一個 "巢狀的" 事務, 它是已經存在事務的一個真正的子事務. 潛套事務開始執行時, 它將取得一個**儲存點(Savepoint)**. 如果這個巢狀事務失敗, 我們將回滾到此  **儲存點**. 潛套事務是外部事務的一部分, 只有外部事務結束後它才會被提交. 由此可見, **Propagation.REQUIRES_NEW**  和  **Propagation.NESTED**  的最大區別在於, **Propagation.REQUIRES_NEW**  完全是一個新的事務, 而  **Propagation.NESTED**  則是外部事務的子事務, 如果外部事務提交, 巢狀事務也會被,這個規則同樣適用於回滾.

**注意: 使用此種事務傳播類型, 需要設定事務管理器的 nestedTransactionAllowed 屬性為 true**

```java
/**
 - TransactionConfig.java
 *
 - 事務組態
 */
@Configuration
@EnableTransactionManagement
public class Transaction {
    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(entityManagerFactory);
        transactionManager.setNestedTransactionAllowed(true);
        return transactionManager;
    }
}
```

# Isolation level

|                            |                                                                                                        |
| -------------------------- | ------------------------------------------------------------------------------------------------------ |
| **隔離層級**               | **說明**                                                                                               |
| ISOLATION_DEFAULT          | 使用底層資料庫預設的隔離層級                                                                           |
| ISOLATION_READ_COMMITTED   | 允許交易讀取其它並行的交易已經送出（Commit）的資料欄位，可以防止 Dirty read 問題                       |
| ISOLATION_READ_UNCOMMITTED | 允許交易讀取其它並行的交易還沒送出的資料，會發生 Dirty、Nonrepeatable、Phantom read 等問題             |
| ISOLATION_REPEATABLE_READ  | 要求多次讀取的資料必須相同，除非交易本身更新資料，可防止 Dirty、Nonrepeatable read 問題                |
| ISOLATION_SERIALIZABLE     | 完整的隔離層級，可防止 Dirty、Nonrepeatable、Phantom read 等問題，會鎖定對應的資料表格，因而有效能問題 |


---
# other

交易超時期間（The transaction timeout period）

有的交易操作可能延續一段很長的時間，交易本身可能關聯到資料表格的鎖定，因而長時間的交易操作會有效能上的問題，對於過長的交易操作，您要考慮回滾（Roll back）交易並要求重新操作，而不是無限時的等待交易完成。  
  
您可以設置交易超時期間，計時是從交易開始時，所以這個設置必須搭配傳播行為PROPAGATION_REQUIRED、PROPAGATION_REQUIRES_NEW、PROPAGATION_NESTED來設置。

```java
import org.springframework.http.client.ClientHttpRequestFactory;
import org.springframework.http.client.SimpleClientHttpRequestFactory;
import org.springframework.web.client.RestTemplate;

public class MyService {
    public void makeRemoteAPICall() {
        // Create a request factory with a timeout of 3 seconds
        ClientHttpRequestFactory factory = new SimpleClientHttpRequestFactory();
        ((SimpleClientHttpRequestFactory) factory).setReadTimeout(3000);
        ((SimpleClientHttpRequestFactory) factory).setConnectTimeout(3000);

        RestTemplate restTemplate = new RestTemplate(factory);

        // Make your API call with restTemplate
        String result = restTemplate.getForObject("your_api_url_here", String.class);
    }
}

```
