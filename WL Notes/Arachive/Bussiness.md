# PreAuth vs Pre-Auth complete (Transaction Type)

## On-us scenario
- SRS/AAS在收到Pre-auth Complete的交易時，會去找回原本的pre-auth transaction，不管pre-auth/pre-auth complete的金額關係，SRS/AAS永遠直接發sales complete給IAS即可。
- AAS/ABO直接用sales complete的金額做後續的商店請款/撥款作業

## Off-us Scenario (Visa/MASTER/NCCC)
SRS/IAS需下列機制去處理不同CARD SCHEME發過來的PreAuth/PreAuth complete/incremental的交易。
- PreAuth amount 不等於 PreAuth complete amount:  
	- IAS需要用preAuth complete的金額做cardholder佔額  
- Incremental
	- IAS需要去調整原始授權交易的金額進行cardHolder的佔額



---

```ad-question
title: Why do some businesses pre-authorize funds (like hotels, restaurants, gas stations)?

This is specifically to ensure sufficient funds are available in the account that the card is linked to by authorizing an electronic transaction with a debit card or credit card and holding this balance as unavailable either until the merchant settles the account, or the hold falls off.

When a merchant swipes a customer's credit card, the point-of-sale connects to the merchant's acquirer, or credit card processor, which verifies that the customer's account is valid and that sufficient funds are available to cover the transaction's cost. At this step, the funds are "held" and deducted from the customer's account but are not yet transferred to the merchant. This is a common practice where the amount being authorized at the swipe may differ from the total charge amount, such as when a customer adds a tip at a table-service restaurant or when a customer uses their card to turn on a gas pump without the final sale total being known.
```


```ad-question
title: What is the difference between a dual message and a single message transaction?

The single message system is most commonly used for transactions with a PIN confirmation, such as card-present and ATM transactions.

The dual message system differs from the single message system which sends both the authorization and settlement requests with a single message.

Dual-message transactions are processed in two steps. The first step involves “authorizing” the transaction by checking with the consumer's bank to make sure funds exist in the cardholder's account. The second step involves the periodic bundling of authorized transactions and sending them to the consumer's bank for posting to the cardholder's account. This process was invented in the 60's and is modeled after the process used to clear and settle paper checks. Most dual message transactions do not require a PIN, but instead require the customer to sign at the POS. Since signatures are not verified real-time by the consumer's bank that issued the card, there is really no way to know when the transaction is fraudulent.

By contrast, single-message transactions require the customer to enter a PIN which is verified real-time by the consumer's bank.  PIN transactions are inherently safer and much less prone to fraud since the consumer's bank is validating the PIN is correct before approving the authorization request. Single-message transactions do not require a settlement file to be sent to the consumer's bank.
```

