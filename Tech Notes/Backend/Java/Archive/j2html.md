[TOC]

# J2Html

## create options

```java
String a = TagCreator.each(list, l -> 
	TagCreator.option(TagCreator.attrs("#" + l.getId()), l.getName())
				.withValue(l.getId().toString())).render();
//<option id="0" value="0">NMAE _0</option>
//<option id="1" value="1">NMAE _1</option>
//<option id="2" value="2">NMAE _2</option>
```

```java
String t = TagCreator.option(TagCreator.attrs("#dasd"), "ASdsad").withCondClass(false, "hello").render();
//<option id="dasd" class="hello">ASdsad</option>
```

