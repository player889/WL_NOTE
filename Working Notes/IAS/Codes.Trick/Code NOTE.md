```java
DaoProvider.instance().dao(auth.getClass().getName()).saveEntity(auth);
```


```ad-note
title: Get All Rules 
~~~
IBizRule rule = this.getRule(itnIdnCde, name);
~~~
~~~SQL
--143
select count(*) from sys_rule_item where itn_idn_cde = 'WLCN' and rule_type = 'VISA'
~~~

```