

# Deployee

## Context Path

**HTMl page**

```javascript
<script type="text/javascript" th:inline="javascript">	  
/*<![CDATA[*/
    ctxPath = /*[[@{/}]]*/ '';
/*]]>*/
	   
console.info(ctxPath);

$.ajaxPrefilter(function( options, originalOptions, jqXHR ) {
    if (!options.crossDomain) {
        options.url = ctxPath + options.url;
        console.log("XX");
        console.log(options.url);
    }
});
function leaveAjax(myData, msg){
    return $.ajax({
        url: "v1/api/leaveJob",
        type: "POST",
        headers: { 
            'Accept': 'application/json',
            'Content-Type': 'application/json' 
        },
```

**application.properties**

```properties
server.servlet.context-path=/successfactor
```

**controller**

```java
@RestController
public class ApiController {

	@Autowired
	private AmazonS3Utils s3;

	@PostMapping(value = "/v1/api/leaveJob", produces = "application/json; charset=utf-8")
```

