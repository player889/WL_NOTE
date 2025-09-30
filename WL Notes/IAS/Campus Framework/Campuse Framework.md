```ad-note
title: Base Code
~~~java
@CampusEvent(topic = "tempXXX", direction = CampusEventFlow.IN)
public class TempReturnEvent extends ApplicationEvent {
	/**
	* Create a new {@code ApplicationEvent}.
	*
	* @param source the object on which the event initially occurred or with
	* which the event is associated (never {@code null})
	*/
	@JsonCreator
	public TempReturnEvent(Object source) {
	super(source);
	}
	private static final long serialVersionUID = 1L;
}
~~~
```

Generator Following Codes
![[Pasted image 20230516101144.png|700]]
![[Pasted image 20230516101437.png]]

# Optimus 　 Campus

```java
@Service
@CampusServiceApi(name = "merchant-acct-bucket", path = "/merchant-acct-bucket")
public class MerchantAcctBucketService {
    @Autowired
    private MerchantAcctBucketRepository merchantAcctBucketRepository;

    @GraphQLMutation(name = "createReversalAdjustBuckets")
    @CampusServiceApiOperation(name = "create", path = "/create", type = ApiOperationType.POST)
    @Operation(tags = "MerchantAcctBucketService", summary = "Auto generate reversal and adjustment bucket.", operationId = "createReversalAdjustBuckets")
    public List<MerchantAcctBucket> createMerchantAcctBuckets(
    }

    @GraphQLMutation(name = "updateReversalAdjustBuckets")
    @CampusServiceApiOperation(name = "update", path = "/update", type = ApiOperationType.PUT)
    @Operation(tags = "MerchantAcctBucketService", summary = "Auto update reversal and adjustment bucket.", operationId = "updateReversalAdjustBuckets")
    public List<MerchantAcctBucket> updateMerchantAcctBuckets(
            @RequestBody() @Valid() @GraphQLArgument(name = "entity") MerchantAcctBucket entity) {
    }

}
```


```java
@GraphQLApi()  
@RestController()  
@RequestMapping(path = "/api/merchant-acct-bucket")  
@Tag(name = "MerchantAcctBucketService", description = "")  
public class MerchantAcctBucketServiceProxy {  
  
    @Autowired()  
    private MerchantAcctBucketService merchantAcctBucketService;  
  
    @PostMapping(path = "/create")  
    @GraphQLMutation(name = "create")  
    @Operation(tags = "MerchantAcctBucketService", summary = "", operationId = "create")  
    public List<MerchantAcctBucket> create(@RequestBody() @Valid() @GraphQLArgument(name = "entity") MerchantAcctBucket entity) {  
        return merchantAcctBucketService.createMerchantAcctBuckets(entity);  
    }  
  
    @PutMapping(path = "/update")  
    @GraphQLMutation(name = "update")  
    @Operation(tags = "MerchantAcctBucketService", summary = "", operationId = "update")  
    public List<MerchantAcctBucket> update(@RequestBody() @Valid() @GraphQLArgument(name = "entity") MerchantAcctBucket entity) {  
        return merchantAcctBucketService.updateMerchantAcctBuckets(entity);  
    }  
}
```


```java
@CampusServiceApiOperation(name = "convertAmountByCurrencyCode", type = ApiOperationType.POST,  
        path = "/convert-amount-currency-code")  
public ConvertedAmount convertAmount(@CampusServiceArgument(name = "sourceAmount", type = ArgumentType.Param) final BigDecimal sourceAmount,  
                                     @CampusServiceArgument(name = "sourceCurrCode", type = ArgumentType.Param) final String sourceCurrCode,  
                                     @CampusServiceArgument(name = "destCurrCode", type = ArgumentType.Param) final String destCurrCode,  
                                     @CampusServiceArgument(name = "exponent", type = ArgumentType.Param) final int exponent) throws RemoteException {  
    val exrate = findExchangeRate(sourceCurrCode, destCurrCode)  
            .orElseThrow(() -> new IllegalStateException(  
                    String.format("Unable to find exchange rate from %s to %s.", sourceCurrCode,  
                            destCurrCode)));  
    // add new condition when srcCurrCode and destCurrCode match currency2toCurrency1 get currency2toCurrency1's rate  
    if(StringUtils.equalsIgnoreCase(sourceCurrCode,exrate.getCurrency1().getIsoCodeAlpha())) {  
        return convertAmount(sourceAmount, exponent, exrate, false);  
    }else {  
        return convertAmount(sourceAmount, exponent, exrate, true);  
    }  
}
```