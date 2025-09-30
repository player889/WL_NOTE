1. Request Timeout will throw `ResourceAccessException `
2. Set ConnectionTimeout on RestTemplate [Ref.](https://medium.com/@cizek.jy/spring-resttemplate-why-the-set-timeout-does-not-work-b75aaee076a3)
   ```java
   @Bean
	public RestTemplate restTemplate() {
	return new RestTemplateBuilder()
		.setConnectTimeout(Duration.ofSeconds(2))
		.setReadTimeout(Duration.ofSeconds(2))
		.build();
	}
	```
3. Add headers message in restTempalte
	```java
	CustomerBean customerBean = new CustomerBean();
	// ...
	
	HttpHeaders headers = new HttpHeaders();
	headers.set("headername", "headervalue");      
	
	HttpEntity<CustomerBean> request = new HttpEntity<>(customerBean, headers);
	
	ResponseBean response = restTemplate.postForObject(url, request, ResponseBean.class); 
	```
