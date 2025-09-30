## sekectKey
KeyvalueMapper return the value of generic type as key, following code show that the purchaseDate (long) becomes the new key for Kstream.
```java
KeyValueMapper<String, Purchase, Long> purchaseDateAsKey = 
	(key, purchase) -> purchase.getPurchaseDate().getTime();

KStream<Long, Purchase> filteredKStream = 
	purchaseKStream.filter((key, purchase) -> purchase.getPrice() > 5.00).selectKey(purchaseDateAsKey);
	
```
## ValueJoiner
![[Pasted image 20230227131442.png]]