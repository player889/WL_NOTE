2022.5.19日發佈==**Spring Boot 2.7**==，對"Spring for GraphQL"進行自動配置和指標支持。
在Spring Boot下使用GraphQL至少需要==**"spring-boot-starter-graphql"**==這個starter。
[此處](https://juejin.cn/post/7159206534868631583)不使用spring官方的starter，改為使用graphql-java-kickstart的starter，編寫更加簡单
因GraphQL是和傳輸協議無關的，這就意味著你可以在Spring Web、Webflux、Websocket、RSocket這些傳輸協議中使用GraphQL。

GraphQL內置了一些標量類型：
1. Int：有符32位整數
2. Float：有符雙精度浮點類型
3. String：UTF-8字符序列
4. ID：代表唯一標識，一般用來獲取對象

根操作類型用來定義如何通過API讀寫數據。在GraphQL中有三種根操作類型：
1. Query：用來讀取數據
2. Mutation：用來創建、更新和刪除數據
3. Subscription：類似於Query，用來查詢數據；Subscription（訂閱）提供一種長時間的操作，當時間改變時數據結果也會有實時變化。

Spring Boot會自動在_src/main/resources/graphql/** _ 目錄尋找結尾為".graphqls" or ".gqls" 的schema文件，我們可以使用_spring.graphql.schema.locations_ 自定義文件的位置，或使用_spring.graphql.schema.file-extensions_ 定製文件的擴展名。

* Spring for GraphQL 支持伺服器通過 ==HTTP、WebSocket== 和 RSocket 處理 GraphQL 請求。

```ad-note 
title: scehma example
collapse: close
~~~graphql
# 對象類型
type Book {
    isbn: ID!
    title: String!
    pages: Int
    author: Author!
}
​
type Author {
    idCardNo: ID!
    name: String!
    age: Int
}
​
#根操作查詢數據
type Query {
    allBooks: [Book]!
    bookByIsbn(isbn: ID): Book
​
}
​
# 根操作創建數據
type Mutation {
    createBook(bookInput: BookInput): Book
    createAuthor(authorInput: AuthorInput): Author
}
​
input BookInput { # 定義方法的入參
    isbn: ID!
    title: String!
    pages: Int
    authorIdCardNo: String
}
​
input AuthorInput { # 定義方法的入參
    idCardNo: ID!
    name: String!
    age: Int
}
​
# 根操作訂閱數據
type Subscription {
    greetings: String
}
~~~
```

我們只需要使用常規的@Controller註解就可以將類標註成控製器，通過Spring for GraphQ的@QueryMapping、@MutationMapping和@SubscriptionMapping註解來標註根操作。和Spring MVC的@RequestMapping類似，Spring for GraphQL也提供了一個@SchemaMapping，通過配置不同的參數，從而達到@QueryMapping、@MutationMapping和@SubscriptionMapping一樣的效果，但是我們還是會使用特定的註解而不是通用的註解。

```ad-note
title: controller example
collapse: close

~~~java
@Controller
public class BookController {  
	private final BookRepository bookRepository;  
	private final AuthorRepository authorRepository;  
	​  
	public BookController(BookRepository bookRepository, AuthorRepository authorRepository) {  
	this.bookRepository = bookRepository;  
	this.authorRepository = authorRepository;  
	}  
	​  
	@QueryMapping  
	public List<Book> allBooks(){  
		return bookRepository.findAll();  
	}  
	​  
	@QueryMapping  
	public Book bookByIsbn(@Argument String isbn){  
		return bookRepository.findByIsbn(isbn);  
	}  
	/*這裡是為了schema文件中，類型Book的author屬性來獲取值的，通過@SchemaMapping註解來設置typeName和field來實現，
	通過這種方式我們可以把級聯多個級別的查詢聚合在一起。
	*/
	@SchemaMapping(typeName = "Book" ,field = "author")  
	public Author getAuthor(Book book){  
		return authorRepository.findByIdCardNo(book.getAuthorIdCardNo());  
	}  
	​  
	@MutationMapping  
	public Book createBook(@Argument BookInput bookInput){  
		Book book = new Book();  
		BeanUtils.copyProperties(bookInput,book);  
	return bookRepository.save(book);  
	}  
}
~~~

~~~java
@Controller
public class AuthorController { private final AuthorRepository authorRepository;  
	public AuthorController(AuthorRepository authorRepository) {  
	this.authorRepository = authorRepository;  
	}  
​  
	@MutationMapping  
	public Author createAuthor(@Argument AuthorInput authorInput){  
		Author author = new Author();  
		BeanUtils.copyProperties(authorInput,author);  
		return authorRepository.save(author);  
	}  
}
~~~

這個控製器是為了演示訂閱的，訂閱需要有websocket的支持。
~~~java
@Controller
public class GreetingController {
    @SubscriptionMapping
    public Flux<String> greetings(){
        return Flux.fromStream(Stream.generate(() -> new String("Hello GraphQL " + UUID.randomUUID())))
                .delayElements(Duration.ofSeconds(5))
                .take(10);
    }
​
}
~~~

這裡的處理是，每五秒向客戶端發送“Hello GraphQL”消息，發送10次。
我們還需指定websocket的端點路徑，此時application.properties內容如下：
~~~yaml
spring.jpa.hibernate.ddl-auto=create-drop
spring.graphql.websocket.path=/graphql
~~~

```

Spring Boot內嵌了一個GraphQL的可視化界面工具GraphiQL來編寫和查詢數據，需要在application.properties中開啟：http://localhost:8080/graphiql
postman支持GraphQL協議，路徑填寫GraphQL的端點：http://localhost:8080/graphql
```yaml
spring.graphql.websocket.path=/graphql
spring.graphql.graphiql.enabled=true
```

[TEST](https://zhuanlan.zhihu.com/p/582598286)

![[Pasted image 20230309160322.png|300]]
![[Pasted image 20230309155113.png|700]]


---

對於 WebSocket 傳輸特定的攔截，您可以創建一個 `WebSocketGraphQlClientInterceptor`：
```java
static class MyInterceptor implements WebSocketGraphQlClientInterceptor {

    @Override
    public Mono<Object> connectionInitPayload() {
        // ... the "connection_init" payload to send
    }

    @Override
    public Mono<Void> handleConnectionAck(Map<String, Object> ackPayload) {
        // ... the "connection_ack" payload received
    }

}
```

將上述攔截器 註冊`GraphQlClientInterceptor`為任何其他攔截器，並使用它來攔截 GraphQL 請求，但請注意最多可以有一個 type 的攔截器`WebSocketGraphQlClientInterceptor`。

請求的文檔String可以定義在局部變量或常量中，也可以通過代碼生成的請求對象生成。
在類路徑下創建帶有擴展名.graphql或.gql下 "graphql-documents/"的文檔文件，並通過文件名引用它們。
例如，projectReleases.graphqlin 的文件 src/main/resources/graphql-documents，其內容為src/main/resources/graphql/project.graphql
```graphql
query projectReleases($slug: ID!) {
    project(slug: $slug) {
        name
        releases {
            version
        }
    }
}
```


```java
String document = "{" +
        "  project(slug:\"spring-framework\") {" +
        "   name" +
        "   releases {" +
        "     version" +
        "   }"+
        "  }" +
        "}";

Mono<Project> projectMono = graphQlClient.document(document) 
        .retrieve("project") //要從中解碼的響應映射中“數據”鍵下的路徑。
        .toEntity(Project.class); 
```

```java
Mono<Project> projectMono = graphQlClient.documentName("projectReleases") 
        .variable("slug", "spring-framework") //提供變量值
        .retrieve()
        .toEntity(Project.class);
```

要啟動訂閱流，使用`retrieveSubscription`which 類似於單個響應但返迴響應流，每個響應都解碼為一些數據：
```java
Flux<String> greetingFlux = client.document("subscription { greetings }")
        .retrieveSubscription("greeting")
        .toEntity(String.class);
```

訂閱流可能以：
-   `SubscriptionErrorException`如果伺服器以包含一個或多個 GraphQL 錯誤的顯式“錯誤”消息結束訂閱。該異常提供對從該消息解碼的 GraphQL 錯誤的訪問。
-   `GraphQlTransportException`例如，`WebSocketDisconnectedException`如果底層連接關閉或丟失，您可以使用`retry`運算符重新建立連接並重新開始訂閱。

檢索只是從每個響應映射中的單個路徑解碼的快捷方式。如需更多控製，請使用該`executeSubscription`方法並直接處理每個響應：
```java
Flux<String> greetingFlux = client.document("subscription { greetings }")
        .executeSubscription()
        .map(response -> {
            if (!response.isValid()) {
                // Request failure...
            }

            ResponseField field = response.field("project");
            if (!field.hasValue()) {
                if (field.getError() != null) {
                    // Field failure...
                }
                else {
                    // Optional field set to null... 
                }
            }

            return field.toEntity(String.class)
        });
```

您創建一個`GraphQlClientInterceptor`通過客戶端攔截所有請求：
```java
static class MyInterceptor implements GraphQlClientInterceptor {

    @Override
    public Mono<ClientGraphQlResponse> intercept(ClientGraphQlRequest request, Chain chain) {
        // ...
        return chain.next(request);
    }

    @Override
    public Flux<ClientGraphQlResponse> interceptSubscription(ClientGraphQlRequest request, SubscriptionChain chain) {
        // ...
        return chain.next(request);
    }

}
```


# GraphQL Java - Execution

```java
GraphQLSchema schema = GraphQLSchema.newSchema()
		.query(queryType)
		.build();

GraphQL graphQL = GraphQL.newGraphQL(schema).build();

ExecutionInput executionInput = ExecutionInput.newExecutionInput().query("query { hero { name } }").build();

ExecutionResult executionResult = graphQL.execute(executionInput);

Object data = executionResult.getData();
List<GraphQLError> errors = executionResult.getErrors();
```
[GrapQL](https://blog.csdn.net/pengdeman/article/details/124780627)

---
GraphQLResolver
![[2023-03-09_161806.png]]

 @GraphQLQuery
![[Pasted image 20230310174542.png]]
---

* Graphql interceptor
* GraphQLQueryResolver - Query
* GraphQLResolver - Use as another Resolver (external, sub query).
* DataLoader 解決 Resolver 重複執行問題
* DataFetcherExceptionHandler **創建自定義異常解析器**
* PreparedDocumentProvider **查詢緩存**

| 方法論據                      | 描述                                                                              |
| ----------------------------- | --------------------------------------------------------------------------------- |
| @Argument                     | 用於訪問綁定到更高級別的類型化對象的命名字段參數。                                |
| @Arguments                    | 用於訪問綁定到更高級別的類型化對象的所有字段參數。                                |
| @ProjectedPayload界面         | 用於通過項目接口訪問字段參數。資源用於訪問字段的源（即父/容器）實例。請參閱來源。 |
| DataLoader                    | 用於訪問DataLoader.DataLoaderRegistry見DataLoader。                               |
| @ContextValue                 | GraphQLContext用於從 main中訪問屬性DataFetchingEnvironment。                      |
| @LocalContextValue            | GraphQLContext用於從本地in訪問屬性DataFetchingEnvironment。                       |
| GraphQLContext                | 用於從DataFetchingEnvironment.                                                    |
| java.security.Principal       | 從 Spring Security 上下文中獲取（如果可用）。                                     |
| @AuthenticationPrincipal      | 用於Authentication#getPrincipal()從 Spring Security 上下文訪問。                  |
| DataFetchingFieldSelectionSet | 用於通過 訪問查詢的選擇集DataFetchingEnvironment。                                |
| Locale,Optional<Locale>       | 對於Locale從DataFetchingEnvironment.                                              |
| DataFetchingEnvironment       | 用於直接訪問底層DataFetchingEnvironment.                                          |

@BatchMaper

批量映射方法支持以下參數：

| 方法論據                | 描述                                                                                                |
| ----------------------- | --------------------------------------------------------------------------------------------------- |
| List<K>                 | 源/父對象。                                                                                         |
| java.security.Principal | 從 Spring Security 上下文中獲取（如果可用）。                                                       |
| @ContextValue           | GraphQLContext用於從of訪問值，該值與來自的BatchLoaderEnvironment上下文相同DataFetchingEnvironment。 |
| GraphQLContext          | 用於訪問來自 的上下文，該上下文與來自 的BatchLoaderEnvironment上下文相同DataFetchingEnvironment。   |
| BatchLoaderEnvironment  | GraphQL Java 中可用的環境到 org.dataloader.BatchLoaderWithContext.                                  |

批量映射方法可以返回：

| 返回類型                                   | 描述                                                                |
| ------------------------------------------ | ------------------------------------------------------------------- |
| Mono<Map<K,V>>                             | 以父對象為鍵，批量加載對象為值的映射。                              |
| Flux<V>                                    | 一系列批量加載的對象，其順序必須與傳遞給方法的源/父對象的順序相同。 |
| Map<K,V>,Collection<V>                     | 命令式變體，例如無需遠程調用。                                      |
| Callable<Map<K,V>>,Callable<Collection<V>> | 異步調用的命令式變體。為此， AnnotatedCo                            |

---

![[Pasted image 20230322160850.png|500]]

DataFetcher
![[Pasted image 20230322161102.png|500]]

![[Pasted image 20230322161156.png|500]]

![[Pasted image 20230322161305.png|500]]

![[Pasted image 20230322161354.png|500]]

![[Pasted image 20230322161418.png|500]]

![[Pasted image 20230322161458.png|500]]

![[Pasted image 20230322161700.png|500]]

![[Pasted image 20230322161853.png|500]]

![[Pasted image 20230322162038.png|500]]

![[Pasted image 20230322162120.png|500]]
---
![[2023-03-08_173922.png|600]]