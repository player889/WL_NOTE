

1. Libs
	1. lib-maven-plugin
		1. 使用GoogleContainerTools下的Jib maven plugin build Spring Boot docker image。[Ref](https://matthung0807.blogspot.com/2020/12/spring-boot-jib-maven-build-docker-image.html)
	2. cyclonedx-maven-plugin
		1. Software Bill of Materials (SBOM)
	3. camunda
		1. [Ref](https://www.cnblogs.com/mzhou19860730/articles/15847036.html)
	4. liquebase
	5. hazelcast
	6. keycloak
	7. graphql
	8. eureka
	9. MyBatis
	10. xxl-job
	11. apollo-client
	12. rocketmq
	13. com.nepxion
	14. Apache Dubbo
	15. hibernate-types
		1. Apache ShardingSphere 
		2. Redis
	16. *Change Data Capture (CDC)*
	17. *Distributed Batch*
2. Report
	1. properies - `ibo-batch/ibo-batch-main/src/main/resources/application.yml`
	2. `@EnableConfigurationProperties`
3. Mybatis
	1. Mybatis-Generator - generatorConfig
	2. [Mapper-CRUD](https://www.jianshu.com/p/ae4534fbb10f)

---- 
1. JPA
	1. JPAQueryFactory
		1. [Ref 1](https://bingdoal.github.io/backend/2021/08/spring-boot-jpa-and-querydsl/)
		2. [Ref 2](https://bbs.huaweicloud.com/blogs/346526)
2. ObjectMapper jacksonObjectMapper = new ObjectMapper().registerModule(new JavaTimeModule());
	1. 對於日期類型為 java.time.LocalDate，還需要添加代碼 mapper.registerModule(new JavaTimeModule())，同時添加相應的依賴 jar 包