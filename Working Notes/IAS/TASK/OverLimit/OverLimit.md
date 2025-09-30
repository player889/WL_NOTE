> If the credit transaction overlimit, stop the authorization and update the account with `overlimit` code in sts_cde in IAS and also send the account status with `overlimit` code to Kafka and when IBO receive the msg from topic, it will update the account status in IBO.

- expiry data = account's end_date
- select \* from status_code where ety_cde = 'ACCT'; --STS_CODE= OLIM

---
> This is pass the transaction information to Issuer
> `IssuerAuthorizationService.java`

---
* CUST001
* ACT001
* 4888880000000020

---

Execute following SQL script.

```SQL
UPDATE auth_sequence_code
SET
    ath_seq_txt = 'check.ExpiryDateAuthCheckStep:10:0:1;check.CurrencyCheckStep:20:0:1;check.CustomerStatusAuthCheckStep:24:0:1;check.CustomerBlockAuthCheckStep:26:0:1;check.AccountStatusAuthCheckStep:30:0:1;check.AccountBlockAuthCheckStep:40:0:1;check.PlasticStatusAuthCheckStep:44:0:1;check.ApplicationStatusAuthCheckStep:46:0:1;check.FirstUsageAuthCheckStep:50:0:1;check.MccAuthCheckStep:55:0:1;vis.Cvv2AuthCheckStep:60:0:1;check.Cvv2RetriesAuthCheckStep:70:0:1;check.PinAuthCheckStep:80:0:1;check.PinRetriesAuthCheckStep:90:0:1;check.ApplicationLimitAuthCheckStep:100:0:1;step.DetermineAccountCreditConfigStep:110:0:1;check.CreditLimitAuthCheckStep:120:0:1;common.UpdateAccountCreditDataStep:160:0:1;check.DetermineAccountVelocityConfigStep:170:0:1;check.BussCaseCheckStep:172:0:1;check.RiskControlCheckStep:173:0:1;check.UsageRateCheckStep:174:0:1;common.UpdateVelocityDataStep:175:0:1;check.RollingExcessiveUsagePeriodStep:180:0:1;check.ExcessiveUsageAuthCheckStep:190:0:1;step.UpdateExcessiveUsageStep:200:0:1'
WHERE ath_seq_cde = 'DFLTDFLT6600' AND itn_idn_cde = 'WLCN'
```

```SQL
update sys_rule_item set filter_expr='{"filter": {"children": [{"name": "isoMessage.mti", "op": "in", "type": "multi", "values": ["0100", "0200", "0120", "0220"]}, {"name": "isoMessage.transactionType", "op": "eq", "type": "single", "value": "00"}, {"name": "isoMessage.panEntryMode", "op": "in", "type": "multi", "values": ["07", "91", "98"]}, {"name": "isoMessage.pinEntryMode", "op": "ne", "type": "single", "value": "2"}, {"name": "isoMessage.terminalEntryCapability", "op": "eq", "type": "single", "value": "6"}, {"name": "isoMessage.terminalType", "op": "eq", "type": "single", "value": "14"}], "op": "and", "type": "group"}}' where sid=62
```

Authorization Simulator

- Need to update all relevant (Correlation) currency at account, customer, account_credit_data

```curl
http://localhost:8096/api/transactions/testAuthorization?cardNo=4888880000000020&expiryDate=2607&traceNbr=041100&countryCode=156&currCode=156&hideTrackData=true&amount=200
```

Simulator File => [[tx4.json]]

> select \* from application where pan_txt = '4888880000000020';  
> select \* from account_credit_data where act_idn_sky ='ACT9C70584E45AB1';  
> update account set atc_sts_cde = 'NORM' where act_idn_sky ='ACT9C70584E45AB1';

```ad-note
title: view Kafka message
collapse: none

docker exec -it kafka kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic AccountStatusEventOUT --from-beginning


```


---

```ad-note
title: new column in IBO
~~~SQL
CTD_STANDALONE_INSTALMENT	NUMBER(30,0)	Yes		161	
CTD_PAYMENT_RETAIL_REVERSAL	NUMBER(30,0)	Yes		162	
CTD_PAYMENT_CASH_REVERSAL	NUMBER(30,0)	Yes		163	
CTD_INSTALMENT	NUMBER(30,0)	Yes		164	
OUTSTD_AUTH_CASH_NUMBER	NUMBER(30,0)	Yes		165	
OUTSTD_AUTH_CASH_BALANCE	NUMBER(30,0)	Yes		166	
~~~
```