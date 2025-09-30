# Spring boot

> 在Spring Boot中，當我們使用了spring-boot-starter-jdbc或spring-boot-starter-data-jpa依賴的時候，框架會自動默認分別注入DataSourceTransactionManager或JpaTransactionManager，所以我們不需要任何額外配置就可以用@Transactional註解進行事務的使用。




# ERROR

when start the spring boot server

> Identify and stop the process that's listening on port 8390 or configure this application to listen on another port.


1.  netstat -aon | findstr :8390
2. taskkill /PID <PID> /F