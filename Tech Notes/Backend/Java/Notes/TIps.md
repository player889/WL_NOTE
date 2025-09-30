## Get method execute time
```java
Instant start = java.time.Instant.now();
Instant finish = Instant.now();
long timeElapsed = Duration.between(start, finish).toMillis();
long sec = TimeUnit.MILLISECONDS.toSeconds(timeElapsed);
```

## Remove item from two List
```java
List<String> projects = projectService.findAllByNameV2(select2Vo.getSearch());
List<String> indiumDb =
 	 opportunityService.findAccessProjecttByUserName(userDetails.getUsername());
for (Iterator<String> it = projects.iterator(); it.hasNext(); ) {
  String project = it.next();
  if (!indiumDb.contains(project)) {
  	it.remove();
  }
}
==> projects.removeIf(project -> !indiumDb.contains(project));
```

## 全形 半形
```java
Transliterator transliterator = Transliterator.getInstance("Halfwidth-Fullwidth");
Transliterator transliterator = Transliterator.getInstance("Fullwidth-Halfwidth");
String converted = transliterator.transliterate("ABC");
```
## 取中文
```java
STRING.subStringByByteArray("中文字", 4, false);//取左邊4個 bytes, 得到 "中文"
```
## Regular Expression
```java
//regex
Pattern pattern = Pattern.compile(regexPattern);
for(String value : values) {
    Matcher matcher = pattern.matcher(value);
    if (matcher.matches()) {
        // your code here
    }
}
```
## Enum
```java
public enum Code {
    CODE_1("string1"),
    CODE_2("string2"),
    CODE_3("string3");

    private static class Holder {
        static Map<String, Code> CODE_MAP = new HashMap<>();
    }

    private final String code;

    private Code(String code) {
        this.code = code;
        Holder.CODE_MAP.put(code, this);
    }

    public String getCode() {
        return this.code;
    }

    public Code convertFromString(String code) {
        return CODE_MAP.get(code);
    }
}

//Find by val
return Arrays.stream(Type.values()).filter(e -> e.s.equals(val)).findFirst().orElseThrow(() -> new IllegalStateException(String.format("Unsupported type %s.", val)));
```
## ResourceBundle - Can't find bundle for base name [XXXX] , locale en_US
```java
//AWS lambda
private final ResourceBundle resourcreBundle = ResourceBundle.getBundle("resources/lambda");
//locals
private final ResourceBundle resourcreBundleLocal = ResourceBundle.getBundle("lambda");
```
## Percent-encoding
```java
import java.io.UnsupportedEncodingException;
import java.net.URLDecoder;
import java.net.URLEncoder;

public class URLEncodeDecode {
	public static void main(String[] args) {
		String url = "http://www.gtwang.org/目錄?var1=中文&var2=spa ce";

		String encodedURL = encode(url);
		System.out.println("Encoded URL: " + encodedURL);

		String decodedURL = decode(encodedURL);
		System.out.println("Decoded URL: " + decodedURL);

	}

	// 百分比解碼函數
	public static String decode(String url) {
		try {
			String prevURL = "";
			String decodeURL = url;
			while (!prevURL.equals(decodeURL)) {
				prevURL = decodeURL;
				decodeURL = URLDecoder.decode(decodeURL, "UTF-8");
			}
			return decodeURL;
		} catch (UnsupportedEncodingException e) {
			return "Error: " + e.getMessage();
		}
	}

	// 百分比編碼函數
	public static String encode(String url) {
		try {
			String encodeURL = URLEncoder.encode(url, "UTF-8");
			return encodeURL;
		} catch (UnsupportedEncodingException e) {
			return "Error: " + e.getMessage();
		}
	}
}
//  String encodedURL = "http%3A%2F%2Fwww.gtwang.org%2F%E7%9B%AE%E9%8C%84%3Fvar1%3D%E4%B8%AD%E6%96%87%26var2%3Dspa+ce";
```
## Duration Date (PT9H30M)
```java
LocalTime.MIN.plus(Duration.parse("PT9H30M")).format(DateTimeFormatter.ofPattern("HHmm"));
//0930
```
##  LocalDateTime
```java
https://it.baiked.com/jdkapi1.8/java/time/format/DateTimeFormatter.html

LocalDateTime currentPoint = LocalDateTime.now()
//2019-11-26T21:14:31.669
    
DateTimeFormatter.ISO_LOCAL_DATE.format(LocalDateTime.parse("2019-11-26T21:08:09"));
//2019-11-26

DateTimeFormatter.ISO_LOCAL_TIME.format(LocalDateTime.parse("2019-11-26T21:08:09"));
//21:08:09
```
## Pagination /w PagedListHolder
```java
//service
PagedListHolder<AccountEntity> page = new PagedListHolder<>(list);
page.setPage(currentPage);
//dataTable
this.data = list.getPageList();
this.iTotalDisplayRecords = list.getSource().size();
this.iTotalRecords = list.getPageList().size();
this.iDisplayStart = map.get("iDisplayLength");
this.sEcho = map.get("sEcho");
```
## Boolean
> javaBean中，要設置或獲取某個property的值，就需要相應的get和set方法，對於primitive和自定義類類型的屬性(如:property)，getter和setter方法就是getProperty和setProperty（第一個字母變大寫，前面再加get或set）。對於類型為 boolean的屬性(不是Boolean)，getter方法還可以寫為isProperty（getProperty仍然可用）。
> 一般來我們用IDE(eclipse,JBuilder,IntelliJ IDEA)的自動生成代碼功能為屬性添加gettter/setter方法時，對於boolean類型，生成的getter方法名都是isProperty，而不是getProperty。
> 所以對於boolean類的屬性，如果有一天你把它手工改成了Boolean類型，那麽就要把相應的getter方法名改為getProperty，否則isProperty方法不會被視為property的gettter方法，就會給後續帶來一系列麻煩。
> ==建議使用 getProperty - PropertyUtils==
## CollectionUtils
```java
String[] arrayA = new String[] { "1", "2", "3", "4", "5" };
String[] arrayB = new String[] { "3", "4", "5", "6", "7" };

List a = Arrays.asList( arrayA );
List b = Arrays.asList( arrayB );

Collection union = CollectionUtils.union( a, b );
Collection intersection = CollectionUtils.intersection( a, b );
Collection disjunction = CollectionUtils.disjunction( a, b );
Collection subtract = CollectionUtils.subtract( a, b );

System.out.println( "A: " + ArrayUtils.toString( a.toArray( ) ) );
System.out.println( "B: " + ArrayUtils.toString( b.toArray( ) ) );
System.out.println( "Union(A,B): " + ArrayUtils.toString( union.toArray( ) ) );
System.out.println( "Intersection(A,B): " + ArrayUtils.toString( intersection.toArray( ) ) );
System.out.println( "Disjunction(A,B): " + ArrayUtils.toString( disjunction.toArray( ) ) );
System.out.println( "Subtract(A,B): " + ArrayUtils.toString( subtract.toArray( ) ) );

輸出結果：

A: {1,2,3,4,5}
B: {3,4,5,6,7}
Union(A,B): {3,2,1,7,6,5,4}
Intersection(A,B): {3,5,4}
Disjunction(A,B): {2,1,7,6}
Subtract(A,B): {1,2}

//Find tow different items in two array
List<String> result = Arrays.asList(oldData.getAccountId().split("@")).stream().filter(item -> {
    return !Arrays.asList(newData.split("@")).contains(item);
}).collect(Collectors.toList());
```
## Property
```java
Class<?> type = PropertyUtils.getPropertyType(account, "name");
System.out.println(type.equals(String.class));

String type = PropertyUtils.getPropertyDescriptor(account, "name").getPropertyType().getTypeName();
//String
```

> `PropertiesLoaderUtils` - **Recommand**
> org.springframework.core.io.support.PropertiesLoaderUtils
## Model
- DAO: UserDAO定義了對table_user表操作的接口，存放各種增刪查改的api
- DO: UserDO 一一對應table_user表所有字段映射的實體類，只有屬性setter和getter方法，建議僅僅用於操作DAO層執行sql時的傳參
- DTO: 數據層傳輸對象,UserDTO用於接受來自UserPO的數據，存放業務需要的字段屬性，比如UserDO有10個字段屬性，UserDTO可能存放這10個字段中的幾個,或者是將UserVO數據轉換成UserDTO，再向UserDO傳輸。
- BO: 存放多個業務需要的字段屬性(可在對象裏面定義其他對象作為屬性)，接受來自多個PO 或 多個DTO 或 DO組合DTO,例如前端需要展示用戶完整的簡歷信息可由UserDTO(或者UserDO)、EduDTO(或者EduDO)、SkillDTO(或者SkillDO)組成一個ResumeBO對象返回前端或者是在各服務層傳輸使用
- VO: 表示層對象,主要用於接收來自前端的數據，將其映射成對象，作用跟BO相反，個人建議前端數據字段屬性應與DTO對象屬性(字段)一致
## Field
```java
 o.getClass().getDeclaredField("createUser");
//refector
import org.apache.commons.lang3.reflect.FieldUtils;
Object id = FieldUtils.readField(dto, "id", true);
```
## Generic
```java
class Cell2<T> {
 private final T value;
 private static List<T> values = new ArrayList<T>(); // illegal
 public Cell(T value) { this.value=value; values.add(value); }
 public T getValue() { return value; }
 public static List<T> getValues() { return values; } // illegal
}

class Cell2<T> {
 private final T value;
 private static List<Object> values = new ArrayList<Object>(); // ok
 public Cell(T value) { this.value=value; values.add(value); }
 public T getValue() { return value; }
 public static List<Object> getValues() { return values; } // ok
}
```
## Download File
### POST Form Submit
```js
function formDownloadFile(fileName, path) {
  if (fileName) {
    var form = $("<form>").attr({
      "style": "display:none",
      "target": "",
      "method": "post",
      "action": '/ClientPortal/downLoadFile/v1/google/' + path
    });
    var fileInput = $("<input>").attr({
      "type": "hidden",
      "id": "fileName",
      "name": "fileName",
      "value": fileName
    })
    $("body").append(form);
    form.append(fileInput);
    form.submit();
    form.remove();
  }
}
```

java - 1
```java
ByteArrayInputStream byteArrayInputStream = googleDriveService.downloadCustomizedFile(fileId);
Resource resource = new InputStreamResource(byteArrayInputStream);

HttpHeaders headers = new HttpHeaders();
headers.add("Content-Disposition",
            String.format("attachment;filename=\"%s", fileName + "." + extension));
headers.add("Cache-Control", "no-cache,no-store,must-revalidate");
headers.add("Pragma", "no-cache");
headers.add("Expires", "0");

return ResponseEntity.ok()
  .headers(headers)
  .contentType(MediaType.parseMediaType("application/pdf"))
  .body(resource);
```

java - 2
```java
InputStream in = new FileInputStream(new File("C:\\Users\\Jay\\Desktop\\image-example.jpg"));
response.setContentType(MediaType.IMAGE_JPEG_VALUE);
IOUtils.copy(in, response.getOutputStream());
```

-------
### Fetch API
```js
downloadFile(folderTxt + "." + extension, path);
async function downloadFile(fileName, path) {

  let urlPath = '/ClientPortal/downLoadFile/v2/google/' + path;
  let response = await fetch(urlPath);
  let blob = await response.blob();
  let a = document.createElement('a');
  let url = window.URL.createObjectURL(blob);
  a.href = url;
  a.download = fileName;
  a.click();
  window.URL.revokeObjectURL(url);
  a.remove();
}
```

```java
@GetMapping("/downLoadFile/v2/google/{fileId}/{fileName}/{extension}")
public ResponseEntity<byte[]> downloadCustomizedV2(
  @PathVariable("fileId") String fileId,
  @PathVariable("fileName") String fileName,
  @PathVariable("extension") String extension, HttpServletResponse response)
  throws Exception {

  byte[] bytes = googleDriveService.downloadCustomizedFileV2(fileId);

  HttpHeaders headers = new HttpHeaders();
  headers.add("Content-Disposition",
              String.format("attachment;filename=\"%s", fileName + "." + extension));
  headers.add("Cache-Control", "no-cache,no-store,must-revalidate");
  headers.add("Pragma", "no-cache");
  headers.add("Expires", "0");

  return new ResponseEntity<>(bytes, headers, HttpStatus.OK);
}
```



## IO
### Read file
![[image-20200507120158062.png|300]]

```java
try (BufferedReader br = new BufferedReader(new InputStreamReader(new ClassPathResource("file/ErrorList.txt").getInputStream()))) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
}
```

```java
InputStream resource = new ClassPathResource("ErrorList.txt").getInputStream();
try (BufferedReader reader = new BufferedReader(new InputStreamReader(resource))) {
    String employees = reader.lines().collect(Collectors.joining("\n"));
    System.out.println(employees);
}
```

## Pass object between Controller
```java
//Controller #1
ModelAndView.addObject("name", user.getName());

//Controller #@
public String register(@ModelAttribute("name") String name)
```


---

##  Hashmap
HashMap采用Entry數組來存儲key-value對，每一個鍵值對組成了一個Entry實體，Entry類實際上是一個單向的鏈表結構，它具有Next指針，可以連接下一個Entry實體，依次來解決Hash沖突的問題，因為HashMap是按照Key的hash值來計算Entry在HashMap中存儲的位置的，如果hash值相同，而key內容不相等，那麽就用鏈表來解決這種hash沖突。
![[114006_HzFL_2927759.png]]

```ad-note
title: 單向鏈表就是通過每個結點的指針指向下一個結點從而鏈接起來的結構，最後一個節點的next指向null
![[Notes/Java/PIC/193331_JljJ_2927759.png]]
```

```ad-note
title: 單向循環鏈表和單向列表的不同是，最後一個節點的next不是指向null，而是指向head節點，形成一個“環"。
![[Notes/Java/PIC/193412_xGR9_2927759.png]]
```

```ad-note
title: 從名字就可以看出，雙向鏈表是包含兩個指針的，pre指向前一個節點，next指向後一個節點，但是第一個節點head的pre指向null，最後一個節點的tail指向null。
![[Notes/Java/PIC/193440_9dt2_2927759.png]]
```

```ad-note
title: 雙向循環鏈表和雙向鏈表的不同在於，第一個節點的pre指向最後一個節點，最後一個節點的next指向第一個節點，也形成一個"環"。而==LinkedList==就是基於雙向循環鏈表設計的。
![[Notes/Java/PIC/193526_9m6M_2927759.png]]
```


# Timeout Exception - CompletableFuture

## supplyAsync

```ad-tip
title: System will return timeout exeception after 5000MS (5s)
collapse: true
~~~
import java.util.concurrent.TimeoutException;

@RestController
public class YourController {

    @GetMapping("/yourEndpoint")
    public ResponseEntity<String> yourEndpoint(@Parameter(name = "param", required = true) @RequestParam String param) {
        // Use CompletableFuture to handle the timeout
        CompletableFuture<String> futureResult = CompletableFuture.supplyAsync(() -> fetchDataFromDB(param));

        try {
            // Set a timeout for the CompletableFuture
            String result = futureResult.get(5000, TimeUnit.MILLISECONDS);

            // If the operation is successful, return a 200 OK response
            return ResponseEntity.ok(result);
        } catch (InterruptedException | ExecutionException | TimeoutException e) {
            // Handle the timeout exception
            return ResponseEntity.status(HttpStatus.REQUEST_TIMEOUT).body("Operation timed out");
        }
    }

    // Simulate fetching data from a database
    private String fetchDataFromDB(String param) {
        // Simulate a potentially time-consuming database operation
        // Replace this with your actual database query logic
        try {
            Thread.sleep(7000); // Simulating a delay longer than the timeout
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }

        return "Data from database for param: " + param;
    }
}
~~~
```

## completeOnTimeout

```ad-tip
title: return another resposne when timout
collapse: true

~~~java
public class Main {

    static void sleep(int millis){
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args){
        CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> {
            sleep(4000);
            return "Hello-Educative";
        });

        long timeOutValue = 1;
        TimeUnit timeUnit = TimeUnit.SECONDS;
        String valueIfTimeOut = "Future timed out";
        completableFuture.completeOnTimeout(valueIfTimeOut, timeOutValue, timeUnit)
                .thenAccept(System.out::println);
        sleep(3000);
    }
}
~~~

```