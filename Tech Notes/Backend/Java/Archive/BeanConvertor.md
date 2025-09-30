# Utils 

## alibaba.fastjson

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import org.hibernate.mapping.KeyValue;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.alibaba.fastjson.TypeReference;
import com.alibaba.fastjson.serializer.JSONLibDataFormatSerializer;
import com.alibaba.fastjson.serializer.SerializeConfig;
import com.alibaba.fastjson.serializer.SerializerFeature;

@SuppressWarnings({ "rawtypes" })
public class FastJsonUtil {

	private FastJsonUtil() {
	}

	private static final SerializeConfig config;

	static {
		config = new SerializeConfig();
		config.put(java.util.Date.class, new JSONLibDataFormatSerializer()); // 使用和json-lib兼容的日期輸出格式
		config.put(java.sql.Date.class, new JSONLibDataFormatSerializer()); // 使用和json-lib兼容的日期輸出格式
	}

	private static final SerializerFeature[] features = { 
        SerializerFeature.QuoteFieldNames, //輸出key時是否使用雙引號,默認為true 
        SerializerFeature.WriteMapNullValue, // 輸出空置字段
        SerializerFeature.WriteNullListAsEmpty, // list字段如果為null，輸出為[]，而不是null
        SerializerFeature.WriteNullNumberAsZero, // 數值字段如果為null，輸出為0，而不是null
        SerializerFeature.WriteNullBooleanAsFalse, // Boolean字段如果為null，輸出為false，而不是null
        SerializerFeature.WriteNullStringAsEmpty // 字符類型字段如果為null，輸出為""，而不是null
	};

	/**
	 * 轉換成字符串
	 *
	 * @param object
	 * @return
	 */
	public static String toJSONString(Object object) {
		return JSON.toJSONString(object, config);
	}

	/**
	 * 轉換成字符串 ,帶有過濾器
	 *
	 * @param object
	 * @return
	 */
	public static String toJSONWithFeatures(Object object) {
		return JSON.toJSONString(object, config, features);
	}

	/**
	 * 轉成bean對象
	 *
	 * @param text
	 * @return
	 */
	public static Object toBean(String text) {
		return JSON.parse(text);
	}

	/**
	 * 轉成具體的泛型bean對象
	 *
	 * @param text
	 * @param clazz
	 * @param <T>
	 * @return
	 */
	public static <T> T toBean(String text, Class<T> clazz) {
		return JSON.parseObject(text, clazz);
	}

	/**
	 * 轉成具體的泛型bean對象
	 * 
	 * @param <T>
	 * @param object
	 * @param clazz
	 * @return
	 */
	public static <T> T toBean(Object object, Class<T> clazz) {
		return FastJsonUtil.toBean(FastJsonUtil.toJSONWithFeatures(object), clazz);
	}

	/**
	 * 轉換為具體的泛型數組Array
	 *
	 * @param text
	 * @param clazz
	 * @param <T>
	 * @return
	 */
	public static <T> Object[] toArray(String text, Class<T> clazz) {
		return JSON.parseArray(text, clazz).toArray();
	}

	/**
	 * 轉換為具體的泛型List
	 * 
	 * @param text
	 * @param clazz
	 * @param <T>
	 * @return
	 */

	public static <T> List<T> toList(String text, Class<T> clazz) {
		return JSON.parseArray(text, clazz);
	}

	/**
	 * 轉換為具體的泛型List
	 * 
	 * @param Object
	 * @param clazz
	 * @param <T>
	 * @return
	 */

	public static <T> List<T> toList(Object object, Class<T> clazz) {
		return JSON.parseArray(FastJsonUtil.toJSONWithFeatures(object), clazz);
	}

	/**
	 * 將javabean轉化為序列化的json字符串
	 *
	 * @param keyvalue
	 * @return
	 */
	public static Object beanToJson(KeyValue keyvalue) {
		String textJson = JSON.toJSONString(keyvalue);
		return JSON.parse(textJson);
	}

	/**
	 * 將string轉化為序列化的json字符串
	 *
	 * @param text
	 * @return
	 */
	public static Object textToJson(String text) {
		return JSON.parse(text);
	}

	/**
	 * json字符串轉化為map
	 *
	 * @param s
	 * @return
	 */

	public static Map stringToMap(String s) {
		return JSONObject.parseObject(s);
	}

	/**
	 * 將map轉化為string
	 *
	 * @param m
	 * @return
	 */
	public static String mapToString(Map m) {
		return JSONObject.toJSONString(m);
	}

	/**
	 * 用fastjson 將jsonString 解析成 List<Map<String,Object>>
	 *
	 * @param jsonString
	 * @return
	 */
	public static List<Map<String, Object>> getListMap(String jsonString) {
		List<Map<String, Object>> list = new ArrayList<>();
		try {
			list = JSON.parseObject(jsonString, new TypeReference<List<Map<String, Object>>>() {
			}.getType());
		} catch (Exception e) {
			e.printStackTrace();
		}
		return list;
	}
}
```

## fasterxml.jackson - recommand

```java
import java.util.ArrayList;
import java.util.List;
import com.fasterxml.jackson.annotation.JsonInclude.Include;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.MapperFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.type.CollectionType;

public class JackSonUtils {

	private static ObjectMapper objectMapper = new ObjectMapper();

	static {
		objectMapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
		objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
		objectMapper.configure(MapperFeature.USE_ANNOTATIONS, false);
		objectMapper.setSerializationInclusion(Include.NON_NULL);
		objectMapper.setSerializationInclusion(Include.NON_EMPTY);
	}

	public static <R, T> List<T> toList(List<R> list, Class<T> clazz) throws Exception {
		String jsonString = JackSonUtils.toString(list);
		CollectionType javaType = objectMapper.getTypeFactory().constructCollectionType(ArrayList.class, clazz);
		return objectMapper.readValue(jsonString, javaType);
	}

	public static String toString(Object o) throws Exception {
		return objectMapper.writeValueAsString(o);
	}

	public static <T> T toBean(Object o, Class<T> clazz) throws Exception {
		return objectMapper.readValue(toString(o), clazz);
	}

	public static <T> T toBean(JsonNode jsonNode, Class<T> clazz) throws Exception {
		return objectMapper.treeToValue(jsonNode, clazz);
	}

	public static JsonNode toJsonNode(Object o) throws Exception {
		return objectMapper.readTree(toString(o));
	}

}
```

## Gson - generic type

```java
public static <T> List<T> rsToList(ResultSet resultSet, Class<T> clazz) throws Exception {
    String result = rsToJsonStr(resultSet);
    JSONArray jsonArray = new JSONObject(result).getJSONArray("results");
    Type typeOfT = TypeToken.getParameterized(List.class, clazz).getType();
    return new Gson().fromJson(jsonArray.toString(), typeOfT);
}
```

# ResultSet

## rs to String

```
public static String rsToJsonStr(ResultSet resultSet) throws Exception {

    final StringWriter stringWriter = new StringWriter();

    SimpleModule module = new SimpleModule().addSerializer(new ResultSetSerializer());

    ObjectMapper objectMapper = new ObjectMapper().registerModule(module);
    ObjectNode objectNode = objectMapper.createObjectNode();

    objectNode.putPOJO("results", resultSet);
    objectMapper.writeValue(stringWriter, objectNode);

    return stringWriter.toString().replaceAll(System.getProperty("line.separator"), "");
}
```

## rs to Beans - w Gson

```java
public static <T> List<T> rsToList(ResultSet resultSet, Class<T> clazz) throws Exception {
    String result = rsToJsonStr(resultSet);
    JSONArray jsonArray = new JSONObject(result).getJSONArray("results");
    Type typeOfT = TypeToken.getParameterized(List.class, clazz).getType();
    return new Gson().fromJson(jsonArray.toString(), typeOfT);
}
```

# Gson

## Generic Type

```java
public class CommonD<T>{
    ...
}
private static <T> void ab(String resp, Class<T> clazz) {
    Type collectionType = TypeToken.getParameterized(CommonD.class, clazz).getType();
    CommonD<SFLeaveContent> ss = new Gson().fromJson(resp, collectionType);
}
ab(resp, SFLeaveContent.class);
```

## none Generic Type

```java
private static void a(String resp) {
    Type collectionType = new TypeToken<CommonD<SFLeaveContent>>() {
    }.getType();
    CommonD<SFLeaveContent> ss = new Gson().fromJson(resp, collectionType);
}
a(resp);
```

## Copy bean with different field name

```java
class User {
    @SerializedName("user_id")
    private int id;
    private String name;

    // getters and setters here
    // .toString
}
```

```java
Map<String,String> apiObject = new HashMap<>();

apiObject.put("user_id","123123");
apiObject.put("name","Bob");

Gson gson = new Gson();
String json = gson.toJson(values);
User user = gson.fromJson(json, User.class);

System.out.println(user); //User{id=123123, name='Bob'}
```

## Gson with bracket

```java
Gson gson = new GsonBuilder().disableHtmlEscaping().create();
```

```ad-note
title: Convert to Object using ObjectMapper

this code performs serialization of the `osgAuth` object into a JSON string while excluding the `_thisEntity` property, and then deserializes the JSON string back into an object of type `com.worldline.optimus.ias.authorization.service.message.OutstandingAuthorization`.
~~~java
final ObjectMapper objectMapper = new ObjectMapper();

@JsonFilter("selfRefFilter")
interface OutstandingAuthorizationMixin {}

objectMapper.addMixIn(OutstandingAuthorization.class, OutstandingAuthorizationMixin.class);

FilterProvider filter = new SimpleFilterProvider()
    .addFilter("selfRefFilter", SimpleBeanPropertyFilter.serializeAllExcept("_thisEntity"));

ObjectWriter writer = objectMapper.writer(filter);
String jsonString = writer.writeValueAsString(osgAuth);

com.worldline.optimus.ias.authorization.service.message.OutstandingAuthorization obj2 = objectMapper.readValue(jsonString, com.worldline.optimus.ias.authorization.service.message.OutstandingAuthorization.class);
~~~

```