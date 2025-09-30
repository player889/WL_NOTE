

現行:
1. spring-boot-test - 2.2.6.RELEASE
2. spring-boot-test-autoconfigure - 2.2.6.RELEASE
3. junit - 5.5.2
4. mockito - 3.1.0
5. apache surefire - 2.22.2


---
1. BDD vs TDD
	1. Cucumber
2. 使用 libs
	1. JUnit：JUnit是Java中最流行的測試框架之壹。它支持自動化測試，提供各種斷言方法和註釋來定義測試用例。
	2. TestNG：TestNG是JUnit的替代品，提供了更多的功能，如支持測試的分組，測試套件等。
	3. Mockito：Mockito是壹個Mocking框架，用於在單元測試中創建虛擬對象，從而簡化依賴管理。
	4. AssertJ：AssertJ提供了壹組更直觀的斷言方法，可以幫助您編寫更易於閱讀和維護的測試用例。
	5. Hamcrest：Hamcrest是壹個框架，它提供了壹組可組合的斷言匹配器，使您可以編寫更靈活的測試用例。
	6. PowerMock：PowerMock是壹個用於Java的Mocking框架，可以模擬靜態方法，構造函數和私有方法，從而擴展了Mockito的功能。
3. Test 覆蓋率
	- JaCoCo：JaCoCo是一个Java代码覆盖率工具，可以生成各种格式的测试覆盖率报告，包括HTML，XML和CSV。
	- Allure：Allure是一个用于生成漂亮测试报告的框架，它支持JUnit，TestNG和Cucumber测试框架。
	- ExtentReports：ExtentReports是一个灵活的测试报告库，可用于JUnit，TestNG和SpecFlow测试框架。
	- ReportNG：ReportNG是一个自定义报告的HTML报告生成器，它支持JUnit和TestNG测试框架。
	- Cucumber Reports：Cucumber Reports是一个可自定义的HTML报告，可以用于Cucumber测试框架。
	- JBehave Reports：JBehave Reports是一个HTML测试报告生成器，可用于JBehave测试框架。
4. 前端是否需要測試
5. 自動化測試

---
* Class - 系統裡多少Class被跑到
* Function - Class裡多少Function被跑到
* Line - Function裡多少程式被跑到

1.  Statement coverage — 程式碼每一行覆蓋
2.  Branch coverage — SUT中的每個if else是不是都有進去過
3.  Condition coverage — 每個會產生true or false的判斷是不是都有跑到過