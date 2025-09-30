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

## ResultSet
### rs to String
```java
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

### rs to Beans - w Gson
```java
public static <T> List<T> rsToList(ResultSet resultSet, Class<T> clazz) throws Exception {
    String result = rsToJsonStr(resultSet);
    JSONArray jsonArray = new JSONObject(result).getJSONArray("results");
    Type typeOfT = TypeToken.getParameterized(List.class, clazz).getType();
    return new Gson().fromJson(jsonArray.toString(), typeOfT);
}
```
### rs to List
```java
public static <T> List<T> rsToList(ResultSet resultSet, Class<T> clazz) throws Exception {
    String result = rsToJsonStr(resultSet);
    JSONArray jsonArray = new JSONObject(result).getJSONArray("results");
    Type typeOfT = TypeToken.getParameterized(List.class, clazz).getType();
    return new Gson().fromJson(jsonArray.toString(), typeOfT);
}
```

## Gson
```java
public class DriveDeserializer implements JsonDeserializer<List<DriveBean>> {

	@Override
	public List<DriveBean> deserialize(JsonElement json, Type typeOfT, JsonDeserializationContext context) {
		List<DriveBean> beans = new ArrayList<>();
		for (JsonElement file : json.getAsJsonArray()) {
			beans.add(new DriveBean(file.getAsJsonObject()));
		}
		return beans;
	}
}


Type type = new TypeToken<List<DriveBean>>() {}.getType();
Gson gson = new GsonBuilder().registerTypeAdapter(type, new DriveDeserializer()).create();
List<DriveBean> l = gson.fromJson(str, type);
```
### Generic Type
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

### none Generic Type

```java
private static void a(String resp) {
    Type collectionType = new TypeToken<CommonD<SFLeaveContent>>() {
    }.getType();
    CommonD<SFLeaveContent> ss = new Gson().fromJson(resp, collectionType);
}
a(resp);
```

### Copy bean with different field name
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

### Gson with bracket
```java
Gson gson = new GsonBuilder().disableHtmlEscaping().create();
```
## JSON String to OBject
```java
public static Map<String, Object> transferJSONStringObjToObj(String jsonString) throws Exception {
	Map<String, Object> imFtpDataMap = null;
	ObjectMapper mapper = new ObjectMapper();
	imFtpDataMap = new HashMap<String, Object>();
	imFtpDataMap = mapper.readValue(jsonString, Map.class);
	return imFtpDataMap;
}
```
## Object to JSON
```java
public static JSONObject transferObjToJSONObj(Object source) throws Exception {
	ObjectMapper mapper = new ObjectMapper();
	JSONObject jsonString = new JSONObject(mapper.writeValueAsString(source));
	return jsonString;
}
```
## Get All Bean properties name
```java
FPKBatchInputParamsVo fc =  new FPKBatchInputParamsVo();
BeanUtilsBean beanTool = BeanUtilsBean.getInstance();
PropertyDescriptor[] targetDescriptors = beanTool.getPropertyUtils().getPropertyDescriptors(fc);
for (int i = 0; i < targetDescriptors.length; i++) {
	String name = targetDescriptors[i].getName();
	System.out.println(name);
}
```
## BeanUtils - Map To Bean / Bean to Map
```java
import java.beans.BeanInfo;
import java.beans.IntrospectionException;
import java.beans.Introspector;
import java.beans.PropertyDescriptor;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.HashMap;
import java.util.Map;

/**
 * JavaBean and map converter.
 * 
 * 
 */
public final class BeanMapUtils {
    
    /**
     * Converts a map to a JavaBean.
     * 
     * @param type type to convert
     * @param map map to convert
     * @return JavaBean converted
     * @throws IntrospectionException failed to get class fields
     * @throws IllegalAccessException failed to instant JavaBean
     * @throws InstantiationException failed to instant JavaBean
     * @throws InvocationTargetException failed to call setters
     */
    public static final Object toBean(Class<?> type, Map<String, ? extends Object> map) 
            throws IntrospectionException, IllegalAccessException,    InstantiationException, InvocationTargetException {
        BeanInfo beanInfo = Introspector.getBeanInfo(type);
        Object obj = type.newInstance();
        PropertyDescriptor[] propertyDescriptors = beanInfo.getPropertyDescriptors();
        for (int i = 0; i< propertyDescriptors.length; i++) {
            PropertyDescriptor descriptor = propertyDescriptors[i];
            String propertyName = descriptor.getName();
            if (map.containsKey(propertyName)) {
                Object value = map.get(propertyName);
                Object[] args = new Object[1];
                args[0] = value;
                descriptor.getWriteMethod().invoke(obj, args);
            }
        }
        return obj;
    }
    
    /**
     * Converts a JavaBean to a map.
     * 
     * @param bean JavaBean to convert
     * @return map converted
     * @throws IntrospectionException failed to get class fields
     * @throws IllegalAccessException failed to instant JavaBean
     * @throws InvocationTargetException failed to call setters
     */
    public static final Map<String, Object> toMap(Object bean)
            throws IntrospectionException, IllegalAccessException, InvocationTargetException {
        Map<String, Object> returnMap = new HashMap<String, Object>();
        BeanInfo beanInfo = Introspector.getBeanInfo(bean.getClass());
        PropertyDescriptor[] propertyDescriptors = beanInfo.getPropertyDescriptors();
        for (int i = 0; i< propertyDescriptors.length; i++) {
            PropertyDescriptor descriptor = propertyDescriptors[i];
            String propertyName = descriptor.getName();
            if (!propertyName.equals("class")) {
                Method readMethod = descriptor.getReadMethod();
                Object result = readMethod.invoke(bean, new Object[0]);
                if (result != null) {
                    returnMap.put(propertyName, result);
                } else {
                    returnMap.put(propertyName, "");
                }
            }
        }
        return returnMap;
    }
}
```

## ObjectsTypeTransferUtils
```java
package com.fet.commerce.batch.parkingfee.common.utility;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;

import org.apache.commons.lang3.StringUtils;
import org.apache.log4j.Logger;
import org.codehaus.jettison.json.JSONArray;
import org.codehaus.jettison.json.JSONObject;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;

/**
 * @className:ObjectsTypeTransferUtils
 * @description: 檔案型式轉換、傳遞參數的型式轉換
 * @author:Create By Ronica Lee
 * @since:2015年12月3日 下午12:14:40
 */

public class ObjectsTypeTransferUtils {
    static Logger processLog = Logger.getLogger(ObjectsTypeTransferUtils.class);

    /**
     * 利用bean來產生一個對應的JSONStringObject
     * 
     * @param source
     *            來源 source 任一想轉JSON物件的來源物件
     * @return String jsonString
     * @throws JsonProcessingException
     *             拋出JsonProcessingException錯誤
     */
    public static String transferObjToJSONStringObj(Object source) throws Exception {
        ObjectMapper mapper = new ObjectMapper();
        String jsonString = mapper.writeValueAsString(source);
        return jsonString;
    }

    /**
     * 利用bean來產生一個對應的JSONSObject
     * 
     * @param source
     *            Source 任一想轉JSON物件的來源物件
     * @return JSONSObject
     */
    public static JSONObject transferObjToJSONObj(Object source) throws Exception {
        ObjectMapper mapper = new ObjectMapper();
        JSONObject jsonString = new JSONObject(mapper.writeValueAsString(source));
        return jsonString;
    }

    /**
     * 利用JSONStringObject 來產生一個對應的Map
     * 
     * @param jsonString
     *            json字串 jsonString 任一想轉Map物件的jsonString
     * @return Map
     */
    @SuppressWarnings("unchecked")
    public static Map<String, Object> transferJSONStringObjToObj(String jsonString) throws Exception {
        Map<String, Object> imFtpDataMap = null;
        ObjectMapper mapper = new ObjectMapper();
        imFtpDataMap = new HashMap<String, Object>();
        imFtpDataMap = mapper.readValue(jsonString, Map.class);
        return imFtpDataMap;
    }

    /**
     * 產生一個TEXT File
     * 
     * @param contentList
     *            contentList &lt;String&gt; contentList fileContent List
     * @param fileName
     *            new file's Name
     * @param filePath
     *            new file's Path
     */
    public static void transferStringObjToFile(List<String> contentList, String fileName, String filePath, String ismkTempDir) throws Exception {
        String realFilePath = null;

        /*
         * if("Y".equals(ismkTempDir)){ //Test filePath File file = new
         * File(filePath); if(!file.isAbsolute()){ //Alternative filePath
         * realFilePath = "D:\\PKFBackUpFiles\\"; } }else{ realFilePath =
         * filePath; }
         */
        realFilePath = filePath;
        File realFile = new File(realFilePath);
        if (!realFile.exists()) {
            realFile.mkdirs();
        }

        // 開始
        PrintWriter fw = new PrintWriter(new File(realFilePath + fileName), "UTF-8");
        BufferedWriter bw = new BufferedWriter(fw);
        for (String content : contentList) {
            bw.write(content);
            bw.newLine();
        }
        bw.flush();
        bw.close();
        processLog.info(String.format("New a file done!" + "File Name[%s], File location[%s]", fileName, realFilePath));
    }

    /**
     * File 轉成List&lt;String&gt;
     * 
     * @param filePath
     *            new file's Path
     * @param fileName
     *            new file's Name
     * @return List&lt;String&gt;
     */
    public static List<String> readFileAsStringList(String filePath, String fileName, String ismkTempDir) throws Exception {
        List<String> fileDataList = new ArrayList<String>();
        String realFilePath = null;

        /*
         * if("Y".equals(ismkTempDir)){ //Test filePath File file = new
         * File(filePath); if(!file.isAbsolute()){ //Alternative filePath
         * realFilePath = "D:\\PKFBImportFiles\\"; } }else{ realFilePath =
         * filePath; }
         */
        realFilePath = filePath;
        File realFile = new File(realFilePath);
        if (!realFile.exists()) {
            realFile.mkdirs();
        }

        // 開始
        FileInputStream fis = new FileInputStream(realFilePath + fileName);
        InputStreamReader isReader = new InputStreamReader(fis, "UTF8");
        String line = null;

        BufferedReader bufReader = new BufferedReader(isReader);
        while ((line = bufReader.readLine()) != null) {
            fileDataList.add(line);
        }
        processLog.info(String.format("New a List<Object> done!" + "File Name[%s], File location[%s]", fileName, realFilePath));
        File deleteFile = new File(realFilePath + fileName);
        if (deleteFile.exists()) {
            deleteFile.delete();
        }
        bufReader.close();
        return fileDataList;
    }

    /**
     * JSONStr for API by HttpClient
     * 
     * @param jsonStr
     *            String
     * @return HashMap&lt;String,Object&gt; <br/>
     *         result =
     *         returnCode&lt;String&gt;,description&lt;String&gt;,data&gt
     *         ;String,ArrayList&gt;
     */
    public static HashMap<String, Object> JsonToHashMap(String jsonStr) throws Exception {
        HashMap<String, Object> result = new HashMap<String, Object>();
        // 如果json字傳為空時，直接回傳不解析Json
        if (StringUtils.isNotBlank(jsonStr)) {
            String returnCode = new JSONObject((new JSONObject(jsonStr)).getJSONObject("result").toString()).getString("returnCode");
            String description = new JSONObject((new JSONObject(jsonStr)).getJSONObject("result").toString()).getString("description");
            // JSONArray dataArray = new JSONObject((new
            // JSONObject(jsonStr)).getJSONObject("result").toString()).getJSONArray("data");

            JSONArray dataArray = (new JSONObject((new JSONObject(jsonStr)).getJSONObject("result").toString()).isNull("data") ? new JSONArray() : new JSONObject((new JSONObject(jsonStr))
                            .getJSONObject("result").toString()).getJSONArray("data"));

            ArrayList<HashMap<String, String>> dataList = new ArrayList<HashMap<String, String>>();

            for (int i = 0; i < dataArray.length(); i++) {
                JSONObject object = dataArray.getJSONObject(i);
                HashMap<String, String> hm = new HashMap<String, String>();

                Iterator it = object.keys();
                while (it.hasNext()) {
                    String key = (String) it.next();
                    String value = object.getString(key);
                    hm.put(key, value);
                }

                dataList.add(hm);
            }

            result.put("returnCode", returnCode);
            result.put("description", description);
            result.put("data", dataList);
        }

        return result;
    }

    /**
     * 利用JSONString 來產生一個對應的JsonNode(For 下拉選單)
     * 
     * @param jsonString
     *            json字串
     * @param codeKind
     *            代碼種類
     * @return JsonNode(下拉選單 內容)
     * @throws Exception
     *             拋出Exception錯誤
     */
    public static String transferJSONStringObjToJsonNode(String jsonString, String codeKind) throws Exception {
        JsonNode carKindSel = null;
        ObjectMapper om = new ObjectMapper();
        JsonNode carKindData = om.readTree(jsonString);

        if ((null != carKindData.get("returnCode")) && (CommonConst.MSG_SUCCESS.equals(carKindData.get("returnCode").asText()))) {
            carKindSel = carKindData.get(codeKind);
        } else {
            return null;
        }
        return carKindSel.toString();
    }

}
```
## Get Class`<T>` from Gerneric class
```java
https://stackoverflow.com/questions/14368339/spring-3-2-autowire-generic-types

public class GenericServiceImpl<T, D, ID extends Serializable> implements GenericService<T, D, ID> {
 
    @Autowired
    private JpaRepository<T, ID> repository;
 
    @Autowired
    private DozerBeanMapper mapper;
 
    protected Class<T> entityClass;
 
    protected Class<D> dtoClass;
 
    @SuppressWarnings("unchecked")
    public GenericServiceImpl() {
        ParameterizedType genericSuperclass = (ParameterizedType) getClass().getGenericSuperclass();
        this.entityClass = (Class<T>) genericSuperclass.getActualTypeArguments()[0];
        this.dtoClass = (Class<D>) genericSuperclass.getActualTypeArguments()[1];
    }
 
    public D findOne(ID id) {
        return mapper.map(repository.findOne(id), dtoClass);
    }
 
    public List<D> findAll() {
        List<D> result = new ArrayList<D>();
        for (T t : repository.findAll()) {
            result.add(mapper.map(t, dtoClass));
        }
        return result;
    }
     
    public void save(D dto) {
        repository.saveAndFlush(mapper.map(dto, entityClass));
    }
 
}
```
## ObjectMapper Settings
*  objectMapper.configure( XXX, x);
	* Feature.ALLOW_UNQUOTED_FIELD_NAMES 