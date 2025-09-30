## Missing BaseWebController.class file at IAS service

`fas:ExclamationCircle`Add lib to pom.xml

```xml
<dependency>
  <groupId>com.worldline</groupId>
  <artifactId>worldline-web</artifactId>
  <version>1.0-SNAPSHOT</version>
</dependency>
```

## Caused by: java.lang.ClassNotFoundException: com.worldline.netty.codec.IMessageCodec at IAS GUI service

![[Pasted image 20240108095644.png]]

> Reimport the maven

#### additional solution

`fas:ExclamationCircle` add dependency `IAS-iso8583`

## hsmAsyncSecurityService

```java
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'hsmAsyncSecurityService': Unsatisfied dependency expressed through field 'hsmService'; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'com.worldline.cardlite.hsm.api.IHsmService' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
```

```xml
<dependency>
	<groupId>com.worldline.cardlite</groupId>
	<artifactId>IAS-security</artifactId>
	<version>1.2-SNAPSHOT</version>
</dependency>
```

```xml
<dependency>
    <groupId>com.worldline.cardlite</groupId>
    <artifactId>IAS-hsm</artifactId>
    <version>1.2-SNAPSHOT</version>
</dependency>
```

![[Pasted image 20230907170110.png]]

![[2023-09-20_120918.png]]

![[Pasted image 20230920111029.png]]

### Problems with class not found

> Error creating bean with name 'dataSyncController': Unsatisfied dependency expressed through field 'dataSyncService'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataSyncService' defined in class path resource [com/worldline/cardlite/ias/service/config/DataSyncServiceConfig.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.worldline.isa.cup.dataSync.service.IDataSyncService]: Factory method 'dataSyncService' threw exception; nested exception is java.lang.NoClassDefFoundError: com/worldline/isa/cup/dataSync/service/IDataSyncService

1. DataSyncServiceConfig.java
   change #85
   run with Shorten command line with `classpath file`

```java
.make().load(Thread.currentThread().getContextClassLoader())
//.make().load(ClassLoader.getSystemClassLoader())
```

2.  Change the shorten command line from `.classpath file` to `JAR manifest`
    ![[Pasted image 20230526144021.png]]

## CC module

java: com.worldline.optimus.cc.common.model.enums.AuthCheckHandling is not abstract and does not override abstract method getDescription() in com.worldline.optimus.plat.core.enums.IEnum

`-Djps.track.ap.dependencies=false`

![[Pasted image 20230921103610.png]]

## Outstanding

```
Caused by: org.springframework.jdbc.BadSqlGrammarException:
### Error querying database.  Cause: java.sql.SQLSyntaxErrorException: ORA-00933: SQL command not properly ended

### The error may exist in file [C:\jay\workspace\optimus-ias-v1.2\IAS\IAS-core\target\classes\mapper\IAS\_gen\_OutstandingAuthorizationMapper.xml]
### The error may involve defaultParameterMap
### The error occurred while setting parameters
### Cause: java.sql.SQLSyntaxErrorException: ORA-00933: SQL command not properly ended

; bad SQL grammar []; nested exception is java.sql.SQLSyntaxErrorException: ORA-00933: SQL command not properly ended
```

![[Pasted image 20230921170109.png]]

## Builder Project

`fas:ExclamationCircle` Delete ${revision} in .m2 folder

## Error when starting the GUI Service

> 1. Reimport the maven project
> 2. do `mvn clean`

## Error: package XXX.XXX.XXX does not exist

<span style="color:red;">`rir:ArrowRight`</span> NettyServerConfig  
On the specific project  
![[Pasted image 20231115105353.png]]

![[Pasted image 20231005160922.png]]

# java: package does not exist

![[Pasted image 20231129105536.png]]

# ERROR when starting the GUI service

#NettyServerConfig #FeignProviderConfig

#${cardlite.ias.service.rpc}.beans.xml

1. Reload Project
2. Generated Sources and Update Folders

![[Pasted image 20240109170115.png]]

![[Pasted image 20240109165328.png]]

# GUI libs cannot find in the package-lock.json file (404)

1. The reason could be the IAS GUI modify the node.js libs (`package-lock.json`)
2. delete the `package-lock.json` file and run command with `npm install`

# GUI service startup issue

1. do the `Generated the source and update folder for all project`
2. Do `Reload all Maven project`
3. restart `IDEA`
