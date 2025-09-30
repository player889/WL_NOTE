![[Pasted image 20230224091246.png]]

WL Report File to CTBC
![[Pasted image 20230224102954.png]]

--- 
現行lib:
1. spring-boot-test - 2.2.6.RELEASE
2. spring-boot-test-autoconfigure - 2.2.6.RELEASE
3. junit - 5.5.2
4. mockito - 3.1.0
5. apache surefire - 2.22.2

**UT**
 - **==TBD Code Coverage percentage==**
 - 報表產出，使用lib如下：
	- OpenClover Plugin
	- Maven Surefire Report
- Base與客製化功能
	- Customized - 撰寫Junit test，並提供程式碼與測試報表給 CTBC
	- Base - 撰寫Junit test，並提供測試報表給CTBC

---
FT
- 提供POSTMAN與Cypress腳本給CTBC
-  UI`ris:ArrowRight` Cypress [Test. Automate. Accelerate.](https://www.cypress.io/)
- API `ris:ArrowRight` Postman