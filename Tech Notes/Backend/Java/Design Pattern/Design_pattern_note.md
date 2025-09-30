## Interface & Abastract
> ## 抽象類
>
> 在java中抽象的關鍵字為`abstract`，抽象類被創造出來就是為了繼承，簡單明了地告訴用戶跟編譯器自己大概是長什么樣子的。例如抽象類申明的語法：
>
> ```java
> abstract class Abc {
>     abstract void fun();
> }
> ```
>
> 抽象類有以下幾個特性：
>
> 1. 抽象方法必須為public、protected（若為private，則不能給子類繼承，子類無法實現該方法，所以無意義），缺省時為public
> 2. 抽象類不能直接用來創建對象，必須由子類繼承並實現其父類方法才能創建對象；
> 3. 抽象類可以繼承抽象類，子類必須復制繼承父類的抽象方法；
> 4. 只要包含一個抽象方法的抽象類，該方法必須要定義成抽象類，不管是否還包含有其他方法。

>## 接口
>
>在java中抽象的關鍵字為`interface`，接口也可以說是一個更加抽象的抽象類，對行為進行抽象，只提供一種形式，並不提供實施的細節。
>接口的語法如下：
>
>```java
>[public] interface InterfaceName {
> 
>}
>```
>
>繼承時采用關鍵字implements：
>
>```java
>class ClassName implements Interface1,Interface2,[....]{
>}
>```
>
>接口有以下幾個特性：
>
>1. 接口可以包含變量，成員變量會被隱式地指定為public static final變量（並且只能是public static final變量，用private修飾會報編譯錯誤）；
>2. 接口可以包含方法，方法會被隱式地指定為public abstract方法且只能是public abstract方法（用其他關鍵字，比如private、protected、static、 final等修飾會報編譯錯誤），並且接口中所有的方法不能有具體的實現，也就是說，接口中的方法必須都是抽象方法；
>3. 一個類可以同時繼承多個接口，且需要實現所繼承接口的所有方法。

>## 抽象類跟接口的區別
>
>1. 抽象類可以提供成員方法的實現細節，而接口中只能存在public abstract 方法；
>2. 抽象類中的成員變量可以是各種類型的，而接口中的成員變量只能是public static final類型的；
>3. 接口中不能含有靜態代碼塊以及靜態方法，而抽象類可以有靜態代碼塊和靜態方法；
>4. 一個類只能繼承一個抽象類，而一個類卻可以實現多個接口。

>## 那麽何時用抽象類？何時用接口呢？
>
>從生活的角度看：
>把編程映射會日常生活進行對照，那麽一個東西，抽象類表示它是什麽，接口表示它能做什麽。舉一個栗子，一個Person，他有眼睛、膚色，這些描述一個人的特征可以定義在抽象類中，而一個人的行為如打籃球，所以這些可以定義在接口中。
>
>```java
>//抽象類Person
>abstract class Person {
>    abstract void eyes();
>    abstract void skin();
>}
>```
>
>```java
>//接口 Action
>public interface Action {
>    void playBasketball();
>}
>```
>
>那麽有個中國人，他不會打籃球，這個類可以這樣寫：
>
>```java
>public class Chinese extends Person {
>    @Override
>    void eyes() {
>        System.out.print("我的眼睛是黑色的");
>    }
>
>    @Override
>    void skin() {
>        System.out.print("我的皮膚是黃色的");
>    }
>}
>```
>
>有個俄羅斯人，他會打籃球，這個類可以這樣寫：
>
>```java
>public class Russian extends Person implements Action{
>    @Override
>    void eyes() {
>        System.out.print("我的眼睛是黑色的");
>    }
>
>    @Override
>    void skin() {
>        System.out.print("我的皮膚是白色的");
>    }
>
>    @Override
>    public void playBasketball() {
>        System.out.print("我能扣籃");
>    }
>}
>```
>
>## 從編程的角度看：
>
>1. 抽象類適合用來定義某個領域的固有屬性，也就是本質，接口適合用來定義某個領域的擴展功能。
>2. ==當需要為一些類提供公共的實現代碼時，應優先考慮抽象類==。因為抽象類中的非抽象方法可以被子類繼承下來，使實現功能的代碼更簡單。
>3. ==當註重代碼的擴展性跟可維護性時，應當優先采用接口==。
>	1. 接口與實現它的類之間可以不存在任何層次關系，接口可以實現毫不相關類的相同行為，比抽象類的使用更加方便靈活;
>	2. 接口只關心對象之間的交互的方法，而不關心對象所對應的具體類。接口是程序之間的一個協議，比抽象類的使用更安全、清晰。一般使用接口的情況更多。
----
## 單例模式

範例:windows Task Manager、Recyle Bin、在Spring中每個Bean默認是單例的(容器管理)、Servlet單例

優點:減少系統開銷

### 惡漢式 - 線程安全、調用效率高、不能延遲加載

```java
public class Singleton {
    //立即加載
    private static Singleton instance = new Singleton();
    
    private SingleTon(){};
    
    //方法沒有同步、效率高
    public static Singleton getInstance(){
        return instance;
    }
}
```

### 懶漢式 - 線程安全、調用效率不高、延遲加載

```java
public class Singleton {
    //不初始化，延遲加載，用的時候再加載
    private static Singleton instance;
    
    private SingleTon(){};
    
    //真正用的時候才加載，當不同線程跑程式IF的時候，會造成錯誤，所以增加synchronized確保資料同步
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
```

### 靜態內部類實現 - 線程安全、效率高、延遲加載(推薦使用)

```java
public class SingletonPattern {
    private SingletonPattern() {
    }
    private static class SingletonPatternHolder {
		//不會立即加載
        private static final SingletonPattern singletonPattern = new SingletonPattern();
    }
    public static SingletonPattern getInstance() {
		//當呼叫的時候才加載、初始化
        return SingletonPatternHolder.singletonPattern;
    }
}
```

### 枚舉實現 - 無延遲加、天然防止反射與序列化漏洞(JVM)

```java
public enum Singleton{
    //enum本身為單例模式
    INSTANCE;

	public void singletonOperation(){
    }
}
Singleton.INSTANCE == Singleton.INSTANCE
```

#### 兩種方法防止反射與反序列化破解

```java
public class Singleton {
   
    private static Singleton instance = new Singleton();
    
    private SingleTon(){
        //(1)防止反射
        if(instance != null){
            throw new RuntimeException():
        }
    }
   
    public static Singleton getInstance(){
        return instance;
    }
    
    //(2)防止反序列化作用
    private Object readResolve() throws Exception{
        return instance;
    }
}
```

#### 多線程效

惡漢式 > 靜態內部類 > 枚舉世 > 懶漢式
可用CountDownLatch輔助測試

## 工廠模式
Open-Closed Principle
Dependence Inversion Principle
Law of Demeter
用工廠方法代替New操作
### 簡單工廠

```java
public class Car {
	public static Car createCar(String type){
		if("BMW".equals(type)){
			return new BMW();
		}else if("BENZ".equals(type)){
			return new BENZ();
		}else{
			return null;
		}
	}
}

Car car1 = Car.createCar("BMW");
Car car2 = Car.createCar("BNEZ");
```

### 工廠方法

符合(開閉原則OCP)

```java
public interface CarFactory 
    Car createCar();
}
```

```java
public class BMWFactory implements carFactory {
    @override
    public Car createCar(){
        return new BMW();
    }
}
```

```java
public class Client {
    public void main(String[] args){
        Car car = new BMWFactory().createCar();
    }
}
```

### 抽象工廠模式

**Factory method** is a creational design pattern which solves the problem of creating product objects without specifying their concrete classes.

**Abstract Factory** is a creational design pattern, which solves the problem of creating entire product families without specifying their concrete classes.

工廠方法升級版，針對多接口處理 EX: BenzFactory、HondaFactory...
- 工廠模式：工廠模式注重的是如何產生一個物件，例如弓箭手訓練營只要負責如果生產出弓箭手。
- 抽像工廠模式：抽像工廠模式注重在產品的抽象關係，像武器與衣服本來是扯不上關係的兩種物品，不過這兩種物品都是屬於同一種冒險者的裝備，因此他們就有了這層抽象關係。
- Spring中IOC容器創建管理Bean對象(Object)
- JDBC中Connection對象(Object)的獲取
- 反射中Class對象(Object)的newInstance

```ad-note
title:  AbstractFactory(Abstract)
~~~java
public abstract class AbstractFactory {

    public abstract Audi createAudi();

    public abstract BMW createBMW();

}
~~~
```

```ad-note
title: JeepFactory
~~~java
public class JeepFactory extends AbstractFactory {
    @Override
    public Audi createAudi() {
        return new AudiJeep();
    }

    @Override
    public BMW createBMW() {
        return new BMWJeep();
    }
}
~~~
```

```ad-note
title: SUVFactory
~~~java
public class SUVFactory extends AbstractFactory {
    @Override
    public Audi createAudi() {
        return new AudiSUV();
    }

    @Override
    public BMW createBMW() {
        return new BMWSUV();
    }
}
~~~
```



```ad-note
title:  Audi(Abstract)
~~~java
public abstract class Audi {

    private String brand;
    private String type;

    public Audi(){
        this.brand = "Audi";
    }

    public void setType(String type) {
        this.type = type;
    }

    public String getBrand() {
        return brand;
    }

    public String getType() {
        return type;
    }
}
~~~
```

```ad-note
title: BMW(Abstract)
~~~java
public abstract class BMW {

    private String brand;
    private String type;

    public BMW(){
        this.brand = "BMW";
    }

    public void setType(String type) {
        this.type = type;
    }

    public String getBrand() {
        return brand;
    }

    public String getType() {
        return type;
    }
}
~~~
```

```ad-note
title: AudiSUV
~~~java
public class AudiSUV extends Audi {

    public AudiSUV() {
        super();
        setType("SUV");
    }
}

public class AudiJeep extends Audi{
    public AudiJeep() {
        super();
        setType("Jeep");
    }
}
~~~
```

```ad-note
title: BMWSUV
~~~java
public class BMWSUV extends BMW {
    public BMWSUV() {
        super();
        setType("SUV");
    }
}

public class BMWJeep extends BMW{
    public BMWJeep() {
        super();
        setType("Jeep");
    }
}
~~~
```

```ad-note
title: Test
~~~java
public class Test {
    @org.junit.jupiter.api.Test
    public void test(){

        AbstractFactory factorySUV = new SUVFactory();
        System.out.println("----- SUV Factory -----");

        Audi suvAudi = factorySUV.createAudi();
        System.out.println(suvAudi.getBrand() + "的" + suvAudi.getType());

        BMW suvBMW = factorySUV.createBMW();
        System.out.println(suvBMW.getBrand() + "的" + suvBMW.getType());



        AbstractFactory factoryJeep = new JeepFactory();
        System.out.println("----- Jeep Factory -----");

        Audi jeepAudi = factoryJeep.createAudi();
        System.out.println(jeepAudi.getBrand() + "的" + jeepAudi.getType());

        BMW jeepBMW = factoryJeep.createBMW();
        System.out.println(jeepBMW.getBrand() + "的" + jeepBMW.getType());


    }
}
~~~
```


```ad-note
title: Output
----- SUV Factory -----
Audi的SUV
BMW的SUV
----- Jeep Factory -----
Audi的Jeep
BMW的Jeep
```

---
### Part2
#### Factory Pattern

```java
public class ShapeFactory {
	
   //use getShape method to get object of type shape 
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }		
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
         
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
         
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      
      return null;
   }
}
```

```java
public class FactoryPatternDemo {

   public static void main(String[] args) {
      ShapeFactory shapeFactory = new ShapeFactory();

      //get an object of Circle and call its draw method.
      Shape shape1 = shapeFactory.getShape("CIRCLE");

      //call draw method of Circle
      shape1.draw();

      //get an object of Rectangle and call its draw method.
      Shape shape2 = shapeFactory.getShape("RECTANGLE");

      //call draw method of Rectangle
      shape2.draw();

      //get an object of Square and call its draw method.
      Shape shape3 = shapeFactory.getShape("SQUARE");

      //call draw method of square
      shape3.draw();
   }
}
```

###### With Value

```java
import java.io.*;      
abstract class Plan{  
    protected double rate;  
    abstract void getRate();  

    public void calculateBill(int units){  
        System.out.println(units*rate);  
    }  
}//end of Plan class.  
```

```java
class  DomesticPlan extends Plan{  
    //@override  
    public void getRate(){  
        rate=3.50;              
    }  
}//end of DomesticPlan class.  
```

###### With Interface

```java
TrainingCamp trainingCamp = new ArcherTrainingCamp();
Adventurer memberA = trainingCamp.trainAdventurer();

trainingCamp = new WarriorTrainingCamp();
Adventurer memberB = trainingCamp.trainAdventurer();     
```

------

#### Abstract Factory - ( extends )

- Multiples of Interface make abstract

```java
public abstract class AbstractFactory {
   public abstract Color getColor(String color);
   public abstract Shape getShape(String shape) ;
}
```

```java
public class ShapeFactory extends AbstractFactory {
   @Override
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
   
   @Override
   public Color getColor(String color) {
      return null;
   }
}
```

```java
public class FactoryProducer {
   public static AbstractFactory getFactory(String choice){
      if(choice.equalsIgnoreCase("SHAPE")){
         return new ShapeFactory();
      } else if(choice.equalsIgnoreCase("COLOR")){
         return new ColorFactory();
      }
      return null;
   }
}
```

```java
public class AbstractFactoryPatternDemo {
   public static void main(String[] args) {
 
      //获取形状工厂
      AbstractFactory shapeFactory = FactoryProducer.getFactory("SHAPE");
 
      //获取形状为 Circle 的对象
      Shape shape1 = shapeFactory.getShape("CIRCLE");
 
      //调用 Circle 的 draw 方法
      shape1.draw();
 
      //获取形状为 Rectangle 的对象
      Shape shape2 = shapeFactory.getShape("RECTANGLE");
 
      //调用 Rectangle 的 draw 方法
      shape2.draw();
      
      //获取形状为 Square 的对象
      Shape shape3 = shapeFactory.getShape("SQUARE");
 
      //调用 Square 的 draw 方法
      shape3.draw();
 
      //获取颜色工厂
      AbstractFactory colorFactory = FactoryProducer.getFactory("COLOR");
 
      //获取颜色为 Red 的对象
      Color color1 = colorFactory.getColor("RED");
 
      //调用 Red 的 fill 方法
      color1.fill();
 
      //获取颜色为 Green 的对象
      Color color2 = colorFactory.getColor("Green");
 
      //调用 Green 的 fill 方法
      color2.fill();
 
      //获取颜色为 Blue 的对象
      Color color3 = colorFactory.getColor("BLUE");
 
      //调用 Blue 的 fill 方法
      color3.fill();
   }
}
```

#### More Example

```java
public interface GUIFactory {
    Button createButton(); //interface
    Checkbox createCheckbox(); //interface
}
```

### Example

#### Factory Demo

```java
private static Gson gson = new Gson();

public static void main(String[] args) {
    Product tv = ProductFactory.getProduct("tv");
    Integer tvInfo = tv.getSize();

    Product game = ProductFactory.getProduct("game");
    Integer gameInfo = game.getSize();

    System.out.println(gson.toJson(tvInfo));
    System.out.println(gson.toJson(gameInfo));

}
```

```java
public static Product getProduct(String product) {
    if (product.equals("tv")) {
        return new Tv();
    } else if (product.equals("game")) {
        return new Game();
    }
    return null;
}
```

#### ABS FACTORY DEMO

```JAVA
 public static void main(String[] args) {
     
     AbsFactory sonyFactory = CompanyFactory.getFactory("sony");
     IProduct sonyTv = sonyFactory.getProduct("tv");
     IProduct sonyGame = sonyFactory.getProduct("game");
     
     System.out.println("sonyTv = " + sonyTv.getPrice());
     System.out.println("sonyGame = " + sonyGame.getPrice());

     AbsFactory appleFactory = CompanyFactory.getFactory("apple");
     IProduct appleMac = appleFactory.getProduct("mac");
     IProduct applePhone = appleFactory.getProduct("phone");

     System.out.println("appleMac = " + appleMac.getPrice());
     System.out.println("applePhone = " + applePhone.getPrice());

 }
```

```JAVA
public class CompanyFactory {
    public static AbsFactory getFactory(String factory) {
        if (factory.equals("apple")) {
            return new AppleFactory();
        } else if (factory.equals("sony")) {
            return new SonyFactory();
        } else {
            return null;
        }

    }
}
```

```JAVA
public class SonyFactory extends AbsFactory {
    @Override
    public IProduct getProduct(String product) {
        if (product.equals("tv")) {
            return new TvFactory();
        } else if (product.equals("game")) {
            return new GameFactory();
        } else {
            return null;
        }

    }
}

```

```JAVA
public class GameFactory implements IProduct {
    @Override
    public Integer getSize() {
        return 55;
    }

    @Override
    public BigDecimal getPrice() {
        return new BigDecimal(15000);
    }
}

```

#### Furthermore

```java
public interface AbstractFactory<T> {
    T create(String animalType) ;
}
```

```java
public class AnimalFactory implements AbstractFactory<Animal> {

    @Override
    public Animal create(String animalType) {
        if ("Dog".equalsIgnoreCase(animalType)) {
            return new Dog();
        } else if ("Duck".equalsIgnoreCase(animalType)) {
            return new Duck();
        }

        return null;
    }

}
```

### Lambda 8 with Factory Pattern

- One of `Supplier` advantages is the case of `Supplier` creating an object : this one is actually created only as the `Supplier.get()` method is invoked.
- Another `Supplier` advantage is its ability to implement factory that may return multiple of things.
- [Suppiler Factory](https://mkyong.com/java8/java-8-supplier-examples/)
```java
Supplier<AppleFactory> shapeFactory = AppleFactory::new;
AppleProduct mac = shapeFactory.get().getShape("mac");
System.out.println(mac.name());
```

```java
public class AppleFactory {

    final static Map<String, Supplier<AppleProduct>> map = new HashMap<>();

    static {
        map.put("mac", Mac::new);
        map.put("phone", Phone::new);
        map.put("tv", Tv::new);
    }

    public AppleProduct getShape(String product) {
        Supplier<AppleProduct> shape = map.get(product);
        if (shape != null) {
	        //使用默認構造函數來實例化Supplier
	        //
            return shape.get();
        }
        throw new IllegalArgumentException("No such product " + product);
    }
}
```

```java
public class Mac implements AppleProduct {

    @Override
    public String name() {
        return "this is mac";
    }
}

```





## 建造者模式

ex: StringBuilder

```java
import java.util.Date;

public abstract class AutoMessage {
    /**
     * 收件人地址
     */
    private String to;
    /**
     * 发件人地址
     */
    private String from;
    /**
     * 标题
     */
    private String subject;
    /**
     * 内容
     */
    private String body;
    /**
     * 发送日期
     */
    private Date sendDate;
    
    public void send() {
        System.out.println("收件人地址：" + to);
        System.out.println("发件人地址：" + from);
        System.out.println("标题：" + subject);
        System.out.println("内容：" + body);
        System.out.println("发送日期：" + sendDate);
    }

    public String getTo() {
        return to;
    }

    public void setTo(String to) {
        this.to = to;
    }

    public String getFrom() {
        return from;
    }

    public void setFrom(String from) {
        this.from = from;
    }

    public String getSubject() {
        return subject;
    }

    public void setSubject(String subject) {
        this.subject = subject;
    }

    public String getBody() {
        return body;
    }

    public void setBody(String body) {
        this.body = body;
    }

    public Date getSendDate() {
        return sendDate;
    }

    public void setSendDate(Date sendDate) {
        this.sendDate = sendDate;
    }
}
```

```java
public class WelcomeMessage extends AutoMessage {
    /**
     * 构造函数
     */
    public WelcomeMessage() {
        System.out.println("发送欢迎信息");
    }
}
```

```java
public class GoodbyeMessage extends AutoMessage {
    /**
     * 构造函数
     */
    public GoodbyeMessage() {
        System.out.println("发送欢送信息");
    }
}
```

```java
import java.util.Date;

public abstract class Builder {
    protected AutoMessage message;
    /**
     * 建造标题零件的方法
     */
    public abstract void buildSubject();
    /**
     * 建造内容零件的方法
     */
    public abstract void buildBody();
    /**
     * 建造收件人零件
     */
    public void buildTo(String to) {
        message.setTo(to);
    }
    /**
     * 建造发件人零件
     */
    public void buildFrom(String from) {
        message.setFrom(from);
    }
    /**
     * 建造发送时间零件
     */
    public void buildSendDate() {
        message.setSendDate(new Date());
    }
    
    /**
     * 邮件构建完成后，调用方法发送邮件
     * 相当于建造者角色产品返回方法。
     */
    public void sendMessge() {
        message.send();
    }
}
```

```java
public class WelcomeBuilder extends Builder {
    public WelcomeBuilder() {
        message = new WelcomeMessage();
    }
    
    @Override
    public void buildSubject() {
        message.setSubject("欢迎标题");
    }

    @Override
    public void buildBody() {
        message.setBody("欢迎内容");
    }

}
```

```java
public class GoodbyeBuilder extends Builder {
    public GoodbyeBuilder() {
        message = new GoodbyeMessage();
    }

    @Override
    public void buildSubject() {
        message.setSubject("欢送标题");
    }

    @Override
    public void buildBody() {
        message.setBody("欢送正文");
    }
}
```

```java
public class Director {
    Builder builder;
    public Director(Builder builder) {
        this.builder = builder;
    }
    
    public void construct(String toAddress, String fromAdress) {
        this.builder.buildTo(toAddress);
        this.builder.buildFrom(fromAdress);
        this.builder.buildSubject();
        this.builder.buildBody();
        this.builder.buildSendDate();
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        Builder builder = new GoodbyeBuilder();
        Director director = new Director(builder);
        director.construct("to@126.com", "from@126.com");
        builder.sendMessge();
    }
}
```

```text
发送欢送信息
收件人地址：to@126.com
发件人地址：from@126.com
标题：欢送标题
内容：欢送正文
发送日期：Thu Jun 22 18:14:55 CST 2017
```

## 原型模式

淺複製

```java

class Prototype implements Cloneable {
	public Prototype clone(){
		Prototype prototype = null;
		try{
			prototype = (Prototype)super.clone();
		}catch(CloneNotSupportedException e){
			e.printStackTrace();
		}
		return prototype; 
	}
}
 
class ConcretePrototype extends Prototype{
	public void show(){
		System.out.println("原型模式实现类");
	}
}
 
public class Client {
	public static void main(String[] args){
		ConcretePrototype cp = new ConcretePrototype();
		for(int i=0; i< 10; i++){
			ConcretePrototype clonecp = (ConcretePrototype)cp.clone();
			clonecp.show();
		}
	}
}
```

深複製 - 序列化方式

```java
//将对象写到流里
ByteArrayOutputStream bos = new ByteArrayOutputStream();
ObjectOutputStream oos = new ObjectOutputStream(bos);
oos.writeObject(this);
//从流里读回来
ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
ObjectInputStream ois = new ObjectInputStream(bis);
Sheep s =  (Sheep) ois.readObject();
```
