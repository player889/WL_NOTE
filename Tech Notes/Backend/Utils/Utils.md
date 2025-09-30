```ad-note
title: Join List with charactor

~~~java
List<String> path = Lists.newArrayList(srcDirectory, packagePath, type.getPostFix().toLowerCase(), type.toClazzName(clazzPreFixName) + ".java");
return org.springframework.util.StringUtils.collectionToDelimitedString(path, File.separator);
~~~


```
