
```java
title: ias-integeration-api

/**  
@Path("")  
public interface IasIBOIntegrationService{  
  
  
  
    /**  
     * 获取卡账客数据 IAS调用IBO     */    @POST  
    @Consumes(MediaType.APPLICATION_JSON)  
    @Produces(MediaType.APPLICATION_JSON)  
    @Path("/api/card-balance-service/retrieve-card-balance-details")  
    GetCardBalanceResponseBean getIBOData(GetCardBalanceRequestBean  request);
```


```java
title:ibo's proxy
@GraphQLApi()  
@RestController()  
@RequestMapping(path = "/api/card-balance")  
@Tag(name = "CardBalanceService", description = "")  
public class CardBalanceServiceProxy {  
  
    @Autowired()  
    private CardBalanceService cardBalanceService;  
  
    @PostMapping(path = "/get")  
    @GraphQLMutation(name = "retrieveCardBalance")  
    @Operation(tags = "CardBalanceService", summary = "", operationId = "retrieveCardBalance")  
    public CardBalance retrieveCardBalance(@RequestBody() @Valid() @GraphQLArgument(name = "cardBalanceRequest") GetCardBalanceRequestDto request) throws RemoteException {  
        return cardBalanceService.retrieveCardBalance(request);  
    }  
}

```

```java
title:ibo
@CampusServiceApi(name = "card", path = "/card-balance")  
public interface CardBalanceService {  
  
    @CampusServiceApiOperation(name = "retrieveCardBalance", type = ApiOperationType.POST, path = "/get")  
    CardBalance retrieveCardBalance(@CampusServiceArgument(name = "cardBalanceRequest") GetCardBalanceRequestDto request) throws RemoteException;  
}

```