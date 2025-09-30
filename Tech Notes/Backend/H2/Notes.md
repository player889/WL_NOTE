
* 預設情況下記憶體資料庫會在程式結束之後被銷燬，如果我們想永久儲存記憶體資料庫需要新增如下配置：
	```properties
	spring.datasource.url=jdbc:h2:file:/data/demo
	```
* 臨時內存存儲
  ```properties
	spring.datasource.url = jdbc:h2:mem:testdb
	```
* Init database
	* schema.sql：初始化表結構
	* data.sql：插入默認數據
