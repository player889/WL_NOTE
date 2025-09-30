```java
//字典順序
list.stream().sorted(Comparator.naturalOrder()).collection(Collectors.toList());
//長度順序
list.stream().sorted(Comparator.comparingInt(String:length)).collection(Collectors.toList());
//長度&字典順序
list.stream().sorted(Comparator.comparingInt(String:length).thenComparing(Comparator.naturalOrder())).collection(Collectors.toList());
```

```java 
//P.67
String forwar = s.toLowerCase().codePoints()
.filter(Character::isLetterOrDigit)
.collect(StringBuilder::new, StringBuilder::appendCodePoint, StringBuilder::append).toString()
```

```java
//P.76
.parallel()
```

```java
//P.83
flapMpa vs Map
```