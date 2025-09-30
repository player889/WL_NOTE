# How to init propery values from database


```java
@Configuration
public class OrderConfig {
    
    @Autowired
    private DataSource dataSource;
    
    @Bean
    public Map<String, String> orderProperties() {
        Map<String, String> properties = new HashMap<>();
        
        try (Connection connection = dataSource.getConnection()) {
            PreparedStatement statement = connection.prepareStatement("SELECT item, description FROM order");
            ResultSet resultSet = statement.executeQuery();
            while (resultSet.next()) {
                String item = resultSet.getString("item");
                String description = resultSet.getString("description");
                properties.put(item, description);
            }
        } catch (SQLException e) {
            // Handle exception
        }
        
        return properties;
    }
}
```


```
myapp.order.items=${orderProperties.keySet()} myapp.order.descriptions=${orderProperties.values()}
```



# PropertyPlaceholderConfigurer

```java
@Configuration
public class PropertiesConfiguration {
	@Bean
	public PropertyPlaceholderConfigurer properties() {
		final PropertyPlaceholderConfigurer ppc = new PropertyPlaceholderConfigurer();
		//ppc.setIgnoreUnresolvablePlaceholders(true);
		ppc.setIgnoreResourceNotFound(true);

		final List<Resource> resourceLst = new ArrayList<Resource>();

		resourceLst.add(new ClassPathResource("myapp_base.properties"));
		resourceLst.add(new FileSystemResource("/etc/myapp/overriding.propertie"));
		resourceLst.add(new ClassPathResource("myapp_test.properties"));
		resourceLst.add(new ClassPathResource("myapp_developer_overrides.properties")); // for Developer debugging.

		ppc.setLocations(resourceLst.toArray(new Resource[]{}));

		return ppc;
	}
}
```

每用一次配置文件中的值，就要聲明一個局部變量。有沒有用代碼的方式，直接讀取配置文件中的值。答案就是重寫PropertyPlaceholderConfigurer
```java
public class PropertyPlaceholder extends PropertyPlaceholderConfigurer {

    private static Map<String,String> propertyMap;

    @Override
    protected void processProperties(ConfigurableListableBeanFactory beanFactoryToProcess, Properties props) throws BeansException {
        super.processProperties(beanFactoryToProcess, props);
        propertyMap = new HashMap<String, String>();
        for (Object key : props.keySet()) {
            String keyStr = key.toString();
            String value = props.getProperty(keyStr);
            propertyMap.put(keyStr, value);
        }
    }

    //static method for accessing context properties
    public static Object getProperty(String name) {
        return propertyMap.get(name);
    }
}
```