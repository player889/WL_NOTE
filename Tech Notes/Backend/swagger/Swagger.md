```ad-note
title: Config
collapse: true

~~~java
@Configuration
public class SwaggerConfig {

    @Bean
    public OpenApiCustomizer sortSchemasAlphabetically() {
        return openApi -> openApi.getComponents().setSchemas(new TreeMap<>(openApi.getComponents().getSchemas()));
    }

    @Bean
    public OpenApiCustomizer globalResponsesCustomizer() {
        return openApi -> {
            openApi.getPaths().forEach((pathName, pathItem) -> {
                pathItem.readOperationsMap().forEach((httpMethod, operation) -> {
                    // @formatter:off
                    ApiResponses apiResponses = operation.getResponses();
                    apiResponses.addApiResponse("401", new ApiResponse().description(ApiResponseDescription.UNAUTHORIZED));
                    apiResponses.addApiResponse("403", new ApiResponse().description(ApiResponseDescription.FORBIDDEN));
                    apiResponses.addApiResponse("404", new ApiResponse().description(ApiResponseDescription.NOT_FOUND));
                    apiResponses.addApiResponse("500", new ApiResponse().description(ApiResponseDescription.INTERNAL_ERROR));
                    apiResponses.addApiResponse("504", new ApiResponse().description(ApiResponseDescription.TIME_OUT));
                    // @formatter:on
                });
            });
        };
    }

    @Bean
    public OpenApiCustomizer customApiHeader() {
        return openApi -> openApi.getPaths().forEach((path, pathItem) -> {
            pathItem.readOperations().forEach(operation -> {
                // @formatter:off
                List<Parameter> globalHeaders =
                        Arrays.asList(
                                new Parameter().$ref("#/components/parameters/header-content-type"),
                                new Parameter().$ref("#/components/parameters/header-content-length"),
                                new Parameter().$ref("#/components/parameters/header-datetime"),
                                new Parameter().$ref("#/components/parameters/header-x-client-system"),

                                new Parameter().$ref("#/components/parameters/header-traceparent"),
                                new Parameter().$ref("#/components/parameters/header-authorization"),
                                new Parameter().$ref("#/components/parameters/header-x-authorization-ap"),
                                new Parameter().$ref("#/components/parameters/header-x-page-size"),
                                new Parameter().$ref("#/components/parameters/header-x-page-number")
                        );
                // @formatter:on
                globalHeaders.forEach(operation::addParametersItem);
            });
        });
    }

    @Bean
    public OpenAPI baseOpenAPI() {
        // @formatter:off
        return new OpenAPI().specVersion(SpecVersion.V31)
                .components(
                        new Components()
                                .addParameters("header-content-type", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string"))
                                        .name("content-type")
                                        .description("REST API should be mainly used in application/json")
                                        .required(true)
                                        .example("application/json"))

                                .addParameters("header-content-length", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string").format("integer"))
                                        .name("content-length")
                                        .description("The payload length")
                                        .required(true)
                                        .example("3495"))

                                .addParameters("header-datetime", new Parameter().in("header")
                                        .schema(new Schema<LocalDateTime>().pattern("YYYY-MM-DDTHH:mm:msZ"))
                                        .name("datetime")
                                        .description("Consumer sent time")
                                        .required(true)
                                        .example("2021-06-01T12:23:00.235Z"))

                                .addParameters("header-x-client-system", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string"))
                                        .name("x-client-system")
                                        .description("The system code corresponding to Consumer")
                                        .required(true)
                                        .example("x-client-system"))

                                .addParameters("header-traceparent", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string"))
                                        .name("traceparent")
                                        .description("W3C Trace context")
                                        .required(true)
                                        .example("00-0af7651916cd43dd8448eb211c80319c-b7ad6b7169203331-01"))

                                .addParameters("header-authorization", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string"))
                                        .name("authorization")
                                        .description("Pass the authentication operations required by API GW (Kong), using Basic Auth mode")
                                        .required(true)
                                        .example("authorization"))

                                .addParameters("header-x-authorization-ap", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string"))
                                        .name("x-authorization-ap")
                                        .description("Security control golden triangle certification, using the Bearer Token model")
                                        .required(true)
                                        .example("eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"))

                                .addParameters("header-x-page-size", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string").format("integer"))
                                        .name("x-page-size")
                                        .description("Number of data in one page")
                                        .example("10"))

                                .addParameters("header-x-page-number", new Parameter().in("header")
                                        .schema(new Schema<String>().type("string").format("integer"))
                                        .name("x-page-number")
                                        .description("Number of pages")
                                        .example("10"))

                                .addSecuritySchemes("bearerAuth", new SecurityScheme().type(SecurityScheme.Type.HTTP)
                                        .scheme("bearer")))
                .addSecurityItem(new SecurityRequirement().addList("bearerAuth"))
                .info(
                        new Info().title("IAS API SPEC")
                        .description("IAS API provide by WLPS")
                        .version(LocalDate.now().format(DateTimeFormatter.BASIC_ISO_DATE))
                );
        // @formatter:on
    }

}

~~~
```

```ad-note
title: Generic response type
collapse: true

![[Pasted image 20250123151928.png]]
```

````ad-note
title: array with example
collapse:true

```ad-tip
title: using @ArraySchema
~~~
@ArraySchema(schema = @Schema(implementation = CustomerControlTypeResp.class, example =
	"[{\"controlType\":\"1\",\"activeFlag\":true,\"startDate\":\"20210601\",\"endDate\":\"20210623\",\"lastModifiedBy\":\"Z1234678\",\"lastModifiedDate\":\"2021-06-01T12:23:00.235Z\"}," +
			"{\"controlType\":\"2\",\"activeFlag\":true,\"startDate\":\"20210601\",\"endDate\":\"20210623\",\"lastModifiedBy\":\"Z1234678\",\"lastModifiedDate\":\"2021-06-01T12:23:00.235Z\"}," +
			"{\"controlType\":\"3\",\"activeFlag\":true,\"startDate\":\"20210601\",\"endDate\":\"20210623\",\"lastModifiedBy\":\"Z1234678\",\"lastModifiedDate\":\"2021-06-01T12:23:00.235Z\"}," +
			"{\"controlType\":\"4\",\"activeFlag\":true,\"startDate\":\"20210601\",\"endDate\":\"20210623\",\"lastModifiedBy\":\"Z1234678\",\"lastModifiedDate\":\"2021-06-01T12:23:00.235Z\"}]"
))
private List<CustomerControlTypeResp> controlTypeList;
~~~
```

```ad-tip
title: using @Schema
~~~
@Schema(  
        implementation = CustomerControlTypeResp.class,  
        example = """  
    [      {        "controlType": "1",        "activeFlag": true,        "startDate": "20210601",        "endDate": "20210623",        "lastModifiedBy": "Z1234678",        "lastModifiedDate": "2021-06-01T12:23:00.235Z"      },      {        "controlType": "2",        "activeFlag": true,        "startDate": "20210601",        "endDate": "20210623",        "lastModifiedBy": "Z1234678",        "lastModifiedDate": "2021-06-01T12:23:00.235Z"      },      {        "controlType": "3",        "activeFlag": true,        "startDate": "20210601",        "endDate": "20210623",        "lastModifiedBy": "Z1234678",        "lastModifiedDate": "2021-06-01T12:23:00.235Z"      },      {        "controlType": "4",        "activeFlag": true,        "startDate": "20210601",        "endDate": "20210623",        "lastModifiedBy": "Z1234678",        "lastModifiedDate": "2021-06-01T12:23:00.235Z"      }    ]    """)  
private List<CustomerControlTypeResp> controlTypeList;
~~~
```


````

```ad-note
title: change swagger's example 
collapse: true


~~~java
    @Bean  
    public OpenApiCustomizer customOpenApi() {  
        Gson gson = new Gson();  
        return openApi -> openApi.getPaths().forEach((path, pathItem) -> {  
            pathItem.readOperations().forEach(operation -> {  
  
                System.out.println("operation.getOperationId() = " + operation.getOperationId());  
  
                ApiResponses responses = operation.getResponses();  
                for (Map.Entry<String, ApiResponse> entry : responses.entrySet()) {  
//                    String statusCode = entry.getKey();  
                    ApiResponse apiResponse = entry.getValue();  
                    Content content = apiResponse.getContent();  
                    if (content != null) {  
                        content.forEach((mediaType, mediaTypeObject) -> {  
                            if (operation.getOperationId().equals("demo_update")) {  
                                MediaType update = content.get("application/json");  
                                System.out.println("gson.toJson(update) = " + gson.toJson(update));  
//                                update.setExample(updateRespExample);  
                            }  
                            if (operation.getOperationId().equals("demo_save")) {  
//                                MediaType update = content.get("application/json");  
//                                update.setExample(saveRespExample);  
                            }  
                        });  
                    }  
                }  
            });  
        });  
    }
~~~


```