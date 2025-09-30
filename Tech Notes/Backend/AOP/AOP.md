AOP 攔截所有 Controller

```java
@RestControllerAdvice(basePackages = {"com.example.quickspringboot.controller"})
public class ControllerResponseAdvice implements ResponseBodyAdvice<Object> {

    //判斷這個類型是不是已經是 R 是了就不用封裝,如果不是就會呼叫 beforeBodyWrite
    @Override
    public boolean supports(MethodParameter returnType, Class<? extends HttpMessageConverter<?>> converterType) {
        return !returnType.getParameterType().isAssignableFrom(R.class);
    }

    @SneakyThrows
    @Override
    public Object beforeBodyWrite(Object body, MethodParameter returnType, MediaType selectedContentType, Class<? extends HttpMessageConverter<?>> selectedConverterType, ServerHttpRequest request, ServerHttpResponse response) {
        // String類型不能直接包裝
        if (returnType.getGenericParameterType().equals(String.class)) {
            ObjectMapper objectMapper = new ObjectMapper();
            try {
                // 將封包裝在ResultVo裡後轉換為json串進行返回
                return objectMapper.writeValueAsString(new R().setData(body));
            } catch (JsonProcessingException e) {
                throw new Exception("ControllerResponseAdvice String 封裝失敗");
            }
        }
        // 否則直接包裝成ResultVo返回
        return new R().setData(body);
    }
}
```
