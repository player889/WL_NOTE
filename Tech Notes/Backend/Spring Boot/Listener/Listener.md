
# [SpringBoot中@EventListener注解的使用](https://segmentfault.com/a/1190000039097608)

監聽事件有兩種方式
第一種： 實現 ApplicationListener
```java
@Component
public RegistrySuccessEventListener implements ApplicationListener<RegistrySuccessEvent> {
    
    @Override
    public void onApplicationEvent(RegistrySuccessEvent registrySuccessEvent) {
        // 事件處理
    }
}
```
第二種： 使用註解 @EventListener
```java
@Components
public class RegistrySuccessEventListener {
 
    @EventListener
    public void onRegistrySuccessEvent(RegistrySuccessEvent registrySuccessEvent) {
        // 事件處理
    }
 
}
```


自訂監聽物件
```java
public class DefinitionEvent extends ApplicationEvent {  
  
    public boolean enable;  
    /**  
     * Create a new ApplicationEvent.  
     *  
     * @param source the object on which the event initially occurred (never {@code null})  
     * @param enable  
     */  
    public DefinitionEvent(Object source, boolean enable) {  
        super(source);  
        this.enable = enable;  
    }
```

監聽&發佈
```java
@Component  
public class DefinitionApplicationListener   
            implements ApplicationListener<ApplicationEvent> {  
  
    // ----------- 簡單使用-實現ApplicationListener 接口  
  
    @Override  
    public void onApplicationEvent(ApplicationEvent event) {  
        System.out.println("事件觸發:" + event.getClass().getName());  
    }  
  
  
    // ----------- 自定義事件以及監聽  
  
    @Autowired  
    private ApplicationEventPublisher eventPublisher;  
  
    /**  
     * 事件發布方法  
     */  
    public void pushListener(String msg) {  
        eventPublisher.publishEvent(new DefinitionEvent(this, false));  
    }  
}
```

@EventListener 註解

除了通過實現接口，還可以使用@EventListener 註解，實現對任意的方法都能監聽事件。
在任意方法上標註@EventListener註解，指定classes ，即需要處理的事件類型，壹般就是ApplicationEvent及其子類，可以設置多項。

```java
@Component  
public class DefinitionAnnotationEventListener {  
  
    @EventListener(classes = {DefinitionEvent.class})  
    public void listen(DefinitionEvent event) {  
        System.out.println("註解監聽器：" + event.getClass().getName());  
    }  
  
}
```

此時，就可以有壹個發布，兩個監聽器監聽到發布的消息了，壹個是註解方式，壹個是非註解方式


# Example

1. 創建監聽實體類UserEvent
```java
public class UserEvent extends ApplicationEvent {

    //需要發送的具體內容
    private String msg;

    public UserEvent(Object source, String msg) {
        super(source);
        this.msg = msg;
    }

    public String getMsg() {
        return msg;
    }
}
```
2. 創建監聽類UserEventListener
```java
@Slf4j
@Component
public class UserEventListener {

    @EventListener(UserEvent.class)
    @Async
    public void userEvent(UserEvent userEvent) {
        log.info("UserEvent:{}", userEvent.getMsg());
    }

}
```
4. 創建controller，發送事件
```java
@RestController
@RequestMapping("event")
public class EventController {

    @Resource
    private ApplicationContext applicationContext;

    @GetMapping
    public void sendEvent() {
        applicationContext.publishEvent(new UserEvent(this, "fisher"));
    }

}
```