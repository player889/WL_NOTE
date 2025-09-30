# Java Reflection

## Reflection with Spring JPA Data interface

```java
public static <T> List<T> iConvertor(List<T> list, Class<T> clazz) throws IllegalAccessException, InvocationTargetException, IntrospectionException, IllegalArgumentException, InstantiationException {
    for (int i = 0; i < list.size(); i++) {
        for (PropertyDescriptor pd : Introspector.getBeanInfo(clazz).getPropertyDescriptors()) {
            if (pd.getReadMethod() != null && !"class".equals(pd.getName())) {
                System.out.println(pd.getName() + ":" + pd.getReadMethod().invoke(list.get(i)));
            }
        }
    }
    return null;
}

List<iOpportuntyInfo> list = repository.initSelect2();
ConvertUtil.iConvertor(list, iOpportuntyInfo.class);
/*
address:收件人地址
amount:1,234.00
billLanguage:繁體
*/
```

## NullPointException - Generic Type

Cause: class with Generic Type is ==null==

For example: ==no== `@Autowired` on serivce