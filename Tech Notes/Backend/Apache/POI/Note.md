# Note

## Read Excel Cell Color

```java
//getRGBWithTint & 0xFF
sb.append("background-color:rgb(" +
(bgColor.getRGBWithTint()[0] & 0xFF) + "," +
(bgColor.getRGBWithTint()[1] & 0xFF) + "," + 
(bgColor.getRGBWithTint()[2] & 0xFF)+ ");");
```

