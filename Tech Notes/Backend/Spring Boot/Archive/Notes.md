```ad-note
title: @ConditionalOnProperty
在spring中有時需要根據配置項來控制某個類或者某個bean是否需要加載.這個時候就可以通過@ConditionnalOnProperty來實現.
[Ref.](https://blog.csdn.net/qq_33790670/article/details/108800037)
```

````ad-note
title: @Convert
collapse: clsoe
- `interface` AttributeConverter
	- convertToDatabaseColumn(X attribute）用於把輸入的類型轉成數據庫存儲的類
	- convertToEntityAttribute(Y dbData) 用於把數據庫搜出來的類型轉成實體中想要的類型
```ad-tip
title: example
collapse: close
~~~java
//Enum Example
public class GenderEnumConverter implements AttributeConverter<GenderEnum,String> {
	@Override
	public String convertToDatabaseColumn(GenderEnum genderEnum) {
		return genderEnum.getCode();
	}

	@Override
	public GenderEnum convertToEntityAttribute(String s) {
	return Arrays.stream(GenderEnum.values())
				.filter(en -> en.getCode().equals(s))
				.findFirst().orElse(null);
	}
}
~~~

~~~java
@Convert( converter = GenderEnumConverter.class)
private GenderEnum gender;
~~~

```

[Ref.](https://blog.csdn.net/wanping321/article/details/82670753)
[Ref.](https://blog.csdn.net/wang__sepcial/article/details/122254923)

````

````ad-note
title: Basic
collapse: close

```ad-tip
title: ModalAndView
collapse: close
`Redirict` will make another request from the client (browser) and change the URL.
`Forward` happens all in the server side, without the need of a additional request, but not changing the URL.
```

````

```ad-note
title: Load Resource File From executable jar with google oauth2
collapse: clsoe
~~~java
// json
private static final String CREDENTIALS = "/google/credentials.json";
InputStream in = getClass().getResourceAsStream(CREDENTIALS);
GoogleClientSecrets clientSecrets = GoogleClientSecrets.load(JSON_FACTORY, new InputStreamReader(in));

// google StoredCredential
private static final String TOKEN_PAHT = "/google_token/directory_prod/";
final java.util.logging.Logger buggyLogger = java.util.logging.Logger.getLogger(FileDataStoreFactory.class.getName());
buggyLogger.setLevel(Level.SEVERE);
ClassPathResource resource = new ClassPathResource(TOKEN_PAHT);
File f = new File(resource.getPath());
return new FileDataStoreFactory(f);
~~~
```

````ad-note
title: Mutiple database
collapse: close


```ad-hint
title: DataSourceConfig
collapse: close
~~~java
@Configuration
public class DataSourceConfig {

	@Bean(name = "primaryDataSource")
	@Primary
	@Qualifier("primaryDataSource")
	@ConfigurationProperties(prefix = "spring.primary.datasource")
	public DataSource primaryDatasource() {
		//		return DataSourceBuilder.create().build();
		return DataSourceBuilder.create().type(HikariDataSource.class).build();
	}
	@Bean(name = "secondaryDataSource")
	@Qualifier("secondaryDataSource")
	@ConfigurationProperties(prefix = "spring.secondary.datasource")
	public DataSource secondaryDataSource() {
		return DataSourceBuilder.create().build();
	}
}
~~~
```

```ad-hint
title: PrimaryConfig
collapse: close
~~~java
@EnableTransactionManagement
@EnableJpaRepositories(entityManagerFactoryRef = "entityManagerFactoryPrimary", //配置連線工廠entityManagerFactory
		transactionManagerRef = "transactionManagerPrimary", //配置事物管理器transactionManager
		basePackages = { "com.indium.repository" }) //設定Repository(持久層)所在位置
@Configuration
public class PrimaryConfig {

	@Autowired
	@Qualifier("primaryDataSource")
	private DataSource primaryDataSource;

	@Primary
	@Bean(name = "entityManagerFactoryPrimary")
	public LocalContainerEntityManagerFactoryBean entityManagerFactoryPrimary(EntityManagerFactoryBuilder builder, @Qualifier("primaryDataSource") DataSource dataSource) {
		return builder.dataSource(dataSource).packages("com.indium.entity").persistenceUnit("primary").build();
	}

	@Primary
	@Bean(name = "transactionManagerPrimary")
	public PlatformTransactionManager transactionManagerPrimary(@Qualifier("entityManagerFactoryPrimary") EntityManagerFactory entityManagerFactory) {
		return new JpaTransactionManager(entityManagerFactory);
	}
}
~~~


```ad-hint
title: SecondaryConfig
collapse: close
~~~java
@EnableTransactionManagement
@EnableJpaRepositories(entityManagerFactoryRef = "entityManagerFactorySecondary", //配置連線工廠entityManagerFactory
		transactionManagerRef = "transactionManagerSecondary", //配置事物管理器transactionManager
		basePackages = { "com.indium.repository.secondary" }) //設定Repository(持久層)所在位置
@Configuration
public class SecondaryConfig {

	@Autowired
	@Qualifier("secondaryDataSource")
	private DataSource secondaryDataSource;

	@Primary
	@Bean(name = "entityManagerFactorySecondary")
	public LocalContainerEntityManagerFactoryBean entityManagerFactorySecondary(EntityManagerFactoryBuilder builder, @Qualifier("secondaryDataSource") DataSource dataSource) {
		return builder.dataSource(dataSource).packages("com.indium.entity.secondary").persistenceUnit("secondary").build();
	}

	@Primary
	@Bean(name = "transactionManagerSecondary")
	public PlatformTransactionManager transactionManagerSecondary(@Qualifier("entityManagerFactoryPrimary") EntityManagerFactory entityManagerFactory) {
		return new JpaTransactionManager(entityManagerFactory);
	}
}
~~~
```


```ad-hint
title: SecondaryConfig
collapse: close
~~~properties
spring.primary.datasource.jdbc-url=jdbc:postgresql://jaytestdb.cgokl5xfngik.us-west-2.rds.amazonaws.com:5432/indium
spring.primary.datasource.username=bonita
spring.primary.datasource.password=bonitasoft
spring.primary.datasource.driver-class-name=org.postgresql.Driver

spring.secondary.datasource.jdbc-url=jdbc:postgresql://jamestestdb.cgokl5xfngik.us-west-2.rds.amazonaws.com:5432/JamesDb
spring.secondary.datasource.username=jamestai1127
spring.secondary.datasource.password=james1127
spring.secondary.datasource.driver-class-name=org.postgresql.Driver
~~~
```

````

```ad-note
title: JpaSpecificationExecutor
collapse: close


~~~java
public interface BaseRepository<T, ID extends Serializable> extends JpaSpecificationExecutor<T>
~~~

~~~java
Specification<OpportunityEntity> spec = new Specification<OpportunityEntity>() {
	private static final long serialVersionUID = -2563881777290842764L;
		@Override
		public Predicate toPredicate(Root<OpportunityEntity> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
		List<Predicate> predicates = new ArrayList<>();
		predicates.add(criteriaBuilder.like(root.get(map.get("searchType")).as(String.class), "%" + map.get("sSearch") + "%"));
		predicates.add(criteriaBuilder.isTrue(root.get("visible").as(Boolean.class)));
		// @formatter:off
		predicates.add(criteriaBuilder.or(
				criteriaBuilder.like(root.get("pm").as(String.class), "%" + userName + "%"),
				criteriaBuilder.like(root.get("pt").as(String.class), "%" + userName + "%"),
				criteriaBuilder.equal(root.get("sv").as(String.class), userName),
				criteriaBuilder.equal(root.get("responsiblePerson").as(String.class), userName),
				criteriaBuilder.equal(root.get("createUser").as(String.class), userName),
				criteriaBuilder.equal(root.get("updateUser").as(String.class), userName),
				criteriaBuilder.equal(root.get("openPerson").as(String.class), userName)
		));
		// @formatter:on
		return query.where(predicates.toArray(new Predicate[predicates.size()])).getRestriction();
		}
};

return repository.findAll(spec, pageable);
~~~
```

```ad-note
title: CriteriaBuilder
collapse: close
~~~java
CriteriaBuilder builder = entityManager.getCriteriaBuilder();
CriteriaQuery<BillingEntity> query = builder.createQuery(BillingEntity.class);
Root<BillingEntity> root = query.from(BillingEntity.class);
if (StringUtils.isBlank(searchTxt)) {
	query.select(root).where(builder.equal(root.<String>get("accountId"), accountId), builder.isTrue(root.get("visible").as(Boolean.class)));
} else {
	query.select(root).where(builder.like(builder.upper(root.<String>get(attributeName)), "%" + searchTxt + "%"), builder.equal(root.<String>get("accountId"), accountId), builder.isTrue(root.get("visible").as(Boolean.class)));
}
query.orderBy(builder.asc(root.<String>get(attributeName)));
return entityManager.createQuery(query).setMaxResults(10).getResultList();
~~~
```

````ad-note
title: File upload
collapse: close

```ad-info
title: java
collapse: close
~~~java
@PostMapping(value = "/opportunity/test")
@ResponseBody
public ResponseEntity<FileVo> uploadFile(@RequestParam("data") String data, @RequestParam(value = "files", required = false) MultipartFile[] multipartfiles, HttpServletRequest req) {
	OpportunityDto modelDTO = JackSonUtils.objectMapper.readValue(data, OpportunityDto.class);
    vo.setFileName(modelDTO.getName());
    vo.setStatus(modelDTO.getMemo());
    if (null != multipartfiles && multipartfiles.length > 0) {
        for (MultipartFile file : multipartfiles) {
            System.out.println("FILE : " + file.getSize());
        }
    }
}
~~~
```

```ad-info
title: html
collapse: close
~~~html
<input type="file" class="form-control" id="files" name="files" multiple="multiple">
~~~
```

```ad-info
title: js
collapse: close
~~~js
$('#opportunitySubmitBtn').on('click', function() {
    var form = $('#opportunityForm')[0];
    let jsonData = $('#opportunityForm').getFormData();
    var formData  = new FormData(form);
    formData.append("data", JSON.stringify(jsonData));
    formData.append("file", $('#files')[0].files[0]);
    $.ajax({
        type: "POST",
        beforeSend(xhr){xhr.setRequestHeader(header,token)},
        enctype: 'multipart/form-data',
        url: "/Indium/opportunity/test",
        data: formData,
        processData: false,
        contentType: false,
        cache: false,
        timeout: 600000,
        success: function (data) {
            console.log("SUCCESS : ", data);
        },
        error: function (e) {
            console.log("ERROR : ", e);
        }
    });
});
~~~
```

````

```ad-note
title: Gradle build Executeable Jar with command
collapse: close
1. gradlew clean
2. gradlew bootJar
3. java -jar -Dspring.profiles.active=dev XXX.jar

~~~gradle
plugins {
    id 'org.springframework.boot' version '2.3.4.RELEASE'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
    id 'java'
    id 'war'
    id 'application'
}

springBoot {
    mainClassName = 'com.ywsmi.arterio.ArterioApplication'
}

group = 'com.ywsmi'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

apply plugin: 'java'
apply plugin: 'application'

mainClassName = 'ArterioApplication'
sourceCompatibility = 1.8
targetCompatibility = 1.8
version = '1.0'

bootWar {
    archiveFileName = "arterio.war"
}

bootJar {
    archiveFileName = "arterio.jar"
}

repositories {
    mavenCentral()
}

configurations {
    compile.exclude module: 'servlet-api'
}

dependencies {

    compile group: 'joda-time', name: 'joda-time', version: '2.10.6'
    annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
    compile group: 'org.apache.commons', name: 'commons-lang3', version: '3.11'
    compile group: 'commons-beanutils', name: 'commons-beanutils', version: '1.9.4'

    providedCompile group: 'javax.servlet', name: 'javax.servlet-api', version: '4.0.1'
    compile 'com.google.api-client:google-api-client:1.20.0'
    compile 'com.google.oauth-client:google-oauth-client-jetty:1.20.0'
    compile 'com.google.apis:google-api-services-admin-directory:directory_v1-rev53-1.20.0'

    compile group: 'org.json', name: 'json', version: '20190722'
    compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.6.2'
    compile group: 'com.google.code.gson', name: 'gson', version: '2.8.6'
    compile group: 'com.fasterxml.jackson.core', name: 'jackson-databind', version: '2.11.2'

    compile group: 'org.springframework.security', name: 'spring-security-oauth2-jose', version: '5.3.3.RELEASE'
    compile group: 'org.springframework.security', name: 'spring-security-oauth2-client', version: '5.3.3.RELEASE'
    compile group: 'com.vladmihalcea', name: 'hibernate-types-52', version: '2.10.0'

    implementation 'org.postgresql:postgresql'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
    testImplementation 'org.springframework.security:spring-security-test'
}

test {
    useJUnitPlatform()
}

//apply from: "task.gradle"
~~~
```

> `ris:ArrowRightCircle`AuditorAware - Set entity create user, update user, create time, update time

```ad-note
title: Run background spring boot jar in ubuntu
collapse: close
![run java in backgroup](https://i.imgur.com/wGjPXP5.png)
```

````ad-note
title: Properties
collapse: close

```ad-tip
title: Override properties
collapse: close
~~~java
//127.0.0.1 is the default value of bootstrapServers column
//The value inside the YAML file wiil overwrite the default value
@Value("${spring.kafka.bootstrap-servers:127.0.0.1:9092}")
private String bootstrapServers;
~~~
```


```ad-tip
title: Map
collapse: close
~~~java
@Value("#{${Google-Company-Group}}")
private Map<String, String> googleCompanyGroup;
~~~
```

```ad-tip
title: List
collapse: close
~~~java
@Value("${Google-Modify-Account}")
private List<String> googleModfiyAccountUser;
~~~

~~~java
//List
@Value("#{'${blog-list}'.split(',')}")
~~~


```java
/*Using @Autowired , Do not using `new RestTemplateUtils()` => the token value will "null";*/

@Component
@PropertySource("classpath:application.properties")
public class RestTemplateUtils {
	@Value("${austin-token}")
	private String token;
}
```

````

```ad-note
title:BaseController
collapse: close
1.baseController不使用註解，提供公共方法；xx1Controller等繼承baseController，同時使用@Controller註解。情況：baseController若不使用spring容器中的資源，此時無問題；若想使用spring容器中的資源，此時將變得復雜，需要通過子類的set註入來獲取資源（類似hibernate的data source註入）。
2. 例子:base controller想要使用spring容器中的uploadService資源。
~~~java
@Controller
@RequestMapping("/brand")
public class BrandController extends BaseController {
    @Autowired
    public void setBaseUploadService(UploadService uploadService){
        super.setUploadService(uploadService);
    }
}
~~~
```
