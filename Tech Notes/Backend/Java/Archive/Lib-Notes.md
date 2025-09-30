- 建構
	- Apache Maven：Maven使用聲明進行構建並進行依賴管理，偏向於使用約定而不是配置進行構建。Maven優於Apache Ant。後者采用了壹種過程化的方式進行配置，所以維護起來相當困難。
	- Gradle：Gradle采用增量構建。Gradle通過Groovy編程而不是傳統的xml聲明進行配置。Gradle可以很好地配合Maven進行依賴管理，並且把Ant腳本當作頭等公民。

- 字節碼操作-編程操作Java字節碼的函數庫。
	- ASM：通用底層字節碼操作及分析。
	- Javassist：嘗試簡化字節碼編輯。
	- Byte Buddy：使用“流式API”進壹步簡化字節碼生成。

- 代碼分析-軟件度量和質量評估工具。
	- Checkstyle：對編程規範和標準進行靜態分析。
	- FindBugs：通過字節碼靜態分析找出潛在Bug。
	- PMD：對源代碼中不良編程習慣進行分析。
	- SonarQube：通過插件集成其它分析組件，提供評估最終結果報告。
	- Safe.ijiami：應用代碼安全，壹鍵檢測漏洞，並最終提供分析報告，包括解決方案，強大的漏洞檢測工具。

- 編譯器-創建分析器、解釋器和編譯器的框架。
	- ANTLR：功能完備的自頂向下分析復雜框架。
	- JavaCC：相對ANTLR更具體，上手略為簡單。支持語法語法超前預測（syntactic lookahead）。

- 持續集成 - 支持持續集成、測試和應用發布的工具。
	- Bamboo：Atlassian的持續集成（CI）解決方案，包含很多其它產品。
	- CircleCI：提供托管服務，可免費試用。
	- Codeship：提供托管服務，提供有限免費計劃。
	- Go：ThoughtWork開源持續集成解決方案。
	- Jenkins：提供基於服務器的部署服務。
	- TeamCity：JetBrain持續集成方案，提供免費版。
	- Travis：提供托管服務，常用於開源項目。

- 數據庫-簡化數據庫交互的工具、庫。
	- Flyway：使用Java API輕松完成數據庫遷移。
	- H2：小型SQL數據庫，以內存操作著稱。
	- JDBI：便捷的JDBC抽象。
	- jOOQ：基於SQL schema生成類型安全代碼。
	- PResto：針對大數據的分布式SQL查詢引擎。
	- Querydsl：針對Java的類型安全統壹查詢。
	- 日期和時間-處理日期和時間的函數庫。
	- Joda-Time：Java 8出現之前，它是日期、時間處理的標準函數庫。
	- Time4J：Java高級日期、時間函數庫。

- 依賴 註入-幫助代碼實現控制反轉模式的函數庫。
	- Dagger ：編譯期的註入框架，沒有使用反射，主要用於Android開發。
	- Guice：輕量級註入框架，功能強大可與Dagger媲美。

- 開發庫-從基礎層次上改進開發流程。
	- aspectJ：面向切面編程擴展，與程序無縫連接。
	- Auto：源代碼生成器集合。
	- DCEVM：通過修改JVM，在運行時可無限次重定義已加載的類。OpenJDK 7、8已提供支持，詳情可查看這個分支（fork）。
	- JRebel：商用軟件，無需重新部署可即時重新加載代碼及配置。
	- Lombok：代碼生成器，旨在減少Java冗余代碼。
	- RxJava：使用JVM中可觀察序列，創建異步、基於事件應用程序的函數庫。
	- Spring Loaded：另壹個JVM類重載代理。
	- vert.x：JVM多語言事件驅動應用框架。

- 分布式應用-用來開發分布式、具有容錯性應用程序的函數庫和框架。
	- Akka：構建並發、分布式和具有容錯功能的事件驅動應用程序所需的工具包和運行時。
	- Apache Storm：分布式實時計算系統。
	- Apache ZooKeeper：為大型分布式系統，使用分布式配置、同步和命名註冊提供協調服務。
	- Hazelcast：分布式、高可擴展性內存網格。
	- Hystrix：為分布式系統提供延遲和容錯處理。
	- JGroups：壹組提供可靠消息傳輸的工具包，可用來創建集群。集群中的節點可互相發送消息。
	- Quasar：為JVM提供輕量級線程和Actor。

- 發布-使用本機格式分發Java應用程序的工具。
	- Bintray：對二進制發布進行版本控制，可與Maven或Gradle配合使用。
	- IzPack：為跨平臺部署建立授權工具。
	- Launch4j：將JAR包裝為小巧的Windows可執行文件。
	- packr：將程序JAR、資源和JVM打包成Windows、linux和Mac OS X的本機文件。

- 文檔處理-用來處理Office格式文檔的函數庫。
	- Apache POI：支持OOXML （XLSX、DOCX、PPTX）以及 OLE2 （XLS, DOC or PPT）格式的文檔。
	- jOpenDocument：處理OpenDocument格式文檔。

- 遊戲 開發-遊戲開發框架。
	- jMonkeyEngine：支持現代3D開發的遊戲引擎。
	- libGDX：全面的跨平臺高級開發框架。
	- LWJGL：抽象了OpenGL、CL、AL等函數庫的健壯框架。
	- ijiami.cn：遊戲開發全方位的加密安全解決方案，針對遊戲app面臨的壹系列安全問題，提前加入安全解決措施，防患於未然。

- GUI-用來創建現代圖形用戶界面的函數庫。
	- JavaFX：Swing的繼承者。
	- Scene Builder：JavaFX虛擬布局工具。

- 高性能-與高性能計算有關的資源，包括集合以及很多具體功能的函數庫。
	- Disruptor：線程間消息函數庫。
	- fastutil：快速緊湊的Java類型安全集合。
	- GS Collections：受Smalltalk啟發的集合框架。
	- hftc：Hash set和hash map。
	- HPPC：基本類型集合。
	- Javolution：針對實時嵌入式系統的函數庫。
	- Trove：基本類型集合。

- 圖像處理-用來幫助創建、評估或操作圖形的函數庫。
	- Picasso：Android下載圖像和圖像緩存函數庫。
	- ZXing：多種格式的壹維、二維條形碼處理函數庫。

- JSON-簡化JSON處理的函數庫。
	- Gson：將Java對象序列化為JSON及反向操作。使用時提供了很好的性能。
	- Jackson：與GSON類似，但如果需要頻繁初始化Jackson庫會帶來性能問題。

- 日誌-記錄應用程序的日誌函數庫。
	- Apache Log4j 2：對之前版本進行了完全重寫。現在的版本具備壹個強大的插件和配置架構。
	- kibana：對日誌進行分析並進行可視化。
	- Logback：Log4j原班人馬作品。被證明是壹個強健的日誌函數庫，通過Groovy提供了很多有意思的配置選項。
	- logstash：日誌文件管理工具。
	- SLF4J：日誌抽象層，需要與某個具體日誌框架配合使用。

- 機器學習-提供具體統計算法的工具。其算法可從數據中學習。
	- Apache Hadoop：對商用硬件集群上大規模數據存儲和處理的開源軟件框架。
	- Apache Mahout：專註協同過濾、聚類和分類的可擴展算法。
	- Apache Spark：開源數據分析集群計算框架。
	- h2o：用作大數據統計的分析引擎。
	- Weka：用作數據挖掘的算法集合，包括從預處理到可視化的各個層次。

- 消息-在客戶端之間進行消息傳遞，確保協議獨立性的工具。
	- Apache ActiveMQ：實現JMS的開源消息代理（broker），可將同步通訊轉為異步通訊。
	- Apache Kafka：高吞吐量分布式消息系統。
	- JBoss HornetQ：清晰、準確、模塊化且方便嵌入的消息工具。
	- JeroMQ：ZeroMQ的純Java實現。

- 網絡-網絡編程函數庫。
	- Netty：構建高性能網絡應用程序開發框架。
	- OkHttp ：壹個Android和Java應用的HTTP+SPDY客戶端。
	- ORM-處理對象持久化的API。
	- EclipseLink：支持許多持久化標準，JPA、JAXB、JCA和SDO。
	- Hibernate：廣泛使用、強健的持久化框架。Hibernate的技術社區非常活躍。
	- Ebean：支持快速數據訪問和編碼的ORM框架。

- PDF-用來幫助創建PDF文件的資源。
	- Apache FOP：從XSL-FO創建PDF。
	- Apache PDFBox：用來創建和操作PDF的工具集。
	- DynamicReports：JasperReports的精簡版。
	- iText：壹個易於使用的PDF函數庫，用來編程創建PDF文件。註意，用於商業用途時需要許可證。
	- JasperReports：壹個復雜的報表引擎。

- REST框架-用來創建RESTful 服務的框架。
	- Dropwizard：偏向於自己使用的Web框架。用來構建Web應用程序，使用了Jetty、Jackson、Jersey和Metrics。
	- Jersey：JAX-RS參考實現。
	- RESTEasy：經過JAX-RS規範完全認證的可移植實現。
	- Retrofit：壹個Java類型安全的REST客戶端。
	- Spark：受到Sinatra啟發的Java REST框架。
	- Swagger：Swagger是壹個規範且完整的框架，提供描述、生產、消費和可視化RESTful Web Service。

- 搜索-文檔索引引擎，用於搜索和分析。
	- Apache Solr ：壹個完全的企業搜索引擎。為高吞吐量通信進行了優化。
	- Elasticsearch：壹個分布式、支持多租戶（multitenant）全文本搜索引擎。提供了RESTful Web接口和無schema的JSON文檔。

- 安全-用於處理安全、認證、授權或會話管理的函數庫。
	- Apache Shiro：執行認證、授權、加密和會話管理。
	- Cryptomator：在雲上進行客戶端跨平臺透明加密。
	- Keycloak：為瀏覽器應用和RESTful Web Service集成SSO和IDM。目前還處於beta版本，但是看起來非常有前途。
	- PicketLink：PicketLink是壹個針對Java應用進行安全和身份認證管理的大型項目（Umbrella Project）。
	- Spring Security：專註認證、授權和多維度攻擊防護框架。
	- ijiami.cn：專業的移動應用安全智能服務，通過安全的加密解決方案，保證app開發者等的利益，有最全面最安全的加密服務。

- 序列化-用來高效處理序列化的函數庫。
	- FlatBuffers：序列化函數庫，高效利用內存，無需解包和解析即可高效訪問序列化數據。
	- Kryo：快速和高效的對象圖形序列化框架。
	- MessagePack：壹種高效的二進制序列化格式。

- 服務器-用來部署應用程序的服務器。
	- Apache Tomcat：針對Servlet和jsp的應用服務器，健壯性好且適用性強。
	- Apache TomEE：Tomcat加Java EE。
	- GlassFish：Java EE開源參考實現，由Oracle資助開發。
	- Jetty：輕量級、小巧的應用服務器，通常會嵌入到項目中。
	- WildFly：之前被稱作JBoss，由Red Hat開發。支持很多Java EE功能。

- 模版引擎-對模板中表達式進行替換的工具。
	- Apache Velocity：提供HTML頁面模板、email模板和通用開源代碼生成器模板。
	- FreeMarker：通用模板引擎，不需要任何重量級或自己使用的依賴關系。
	- Handlebars.java：使用Java編寫的模板引擎，邏輯簡單，支持語義擴展（semantic - Mustache）。
	- JavaServer Pages：通用網站模板，支持自定義標簽庫。
	- Thymeleaf：旨在替換JSP，支持XML文件。

- 測試-測試內容從對象到接口，涵蓋性能測試和基準測試工具。
	- Apache JMeter：功能性測試和性能評測。
	- Arquillian：集成測試和功能行測試平臺，集成Java EE容器。
	- AssertJ：支持流式斷言提高測試的可讀性。
	- JMH：JVM微基準測試工具。
	- JUnit：通用測試框架。
	- Mockito：在自動化單元測試中創建測試對象，為TDD或BDD提供支持。
	- Selenium：為Web應用程序提供可移植軟件測試框架。
	- Selenide：為Selenium提供精準的周邊API，用來編寫穩定且可讀的UI測試。
	- TestNG ：測試框架。
	- VisualVM：提供可視化方式查看運行中的應用程序信息。

- 工具類-通用工具類函數庫。
	- Apache Commons：提供各種用途的函數，比如配置、驗證、集合、文件上傳或XML處理等。
	- Guava：集合、緩存、支持基本類型、並發函數庫、通用註解、字符串處理、I/O等。
	- javatuples：正如名字表示的那樣，提供tuple支持。盡管目前tuple的概念還有留有爭議。

- 網絡爬蟲-用於分析網站內容的函數庫
	- Apache Nutch ：可用於生產環境的高度可擴展、可伸縮的網絡爬蟲。
	- Crawler4j：簡單的輕量級爬蟲。
	- JSoup ：刮取、解析、操作和清理HTML。

- Web框架-用於處理Web應用程序不同層次間通訊的框架。
	- Apache Tapestry：基於組件的框架，使用Java創建動態、強健的、高度可擴展的Web應用程序。
	- Apache Wicket：基於組件的Web應用框架，與Tapestry類似帶有狀態顯示GUI。
	- Google Web Toolkit：壹組Web開發工具集，包含在客戶端將Java代碼轉為Javascript的編譯器、XML解析器、RCP API、JUnit集成、國際化支持和GUI控件。
	- Grails：Groovy框架，旨在提供壹個高效開發環境，使用約定而非配置、沒有XML並支持混入（mixin）。
	- Play： 使用約定而非配置，支持代碼熱加載並在瀏覽器中顯示錯誤。
	- PrimeFaces：JSF框架，提供免費版和帶技術支持的商業版。包含壹些前端組件。
	- Spring Boot：微框架，簡化了Spring新程序的開發過程。
	- Spring：旨在簡化Java EE的開發過程，提供依賴註入相關組件並支持面向切面編程。
	- Vaadin：基於GWT構建的事件驅動框架。使用服務端架構，客戶端使用Ajax。
	- Ninja：Java全棧Web開發框架。非常穩固、快速和高效。
	- Ratpack：壹組Java開發函數庫，用於構建快速、高效、可擴展且測試完備的HTTP應用程序。

- 網站
	- Google Java Style
	- InfoQ
	- Java Code Geeks
	- Java.net
	- Javalobby
	- JavaWorld
	- RebelLabs
	- The Java Specialist’ Newsletter
	- TheServerSide.com
	- Thoughts On Java
	- ImportNew（ImportNew 專註 Java 技術）
	- ijiami.cn