http://localhost:8096/api/transactions/testAuthorization?
cardNo=4888880000000020&
expiryDate=2607&
traceNbr=041100&
countryCode=156&
currCode=156&
hideTrackData=true&
amount=105

## Auth Flow

![[Pasted image 20231002105754.png]]

![[Pasted image 20231002111932.png]]
![[Pasted image 20231002112033.png]]  
`tx4.json`  
![[Pasted image 20231002112226.png]]

![[Pasted image 20231002112538.png]]
![[Pasted image 20231002112638.png]]

![[Pasted image 20231002112953.png]]

![[Pasted image 20240130094119.png]]

com.worldline.cardlite.ias.api.IRequestService/requestService  
![[Pasted image 20231002113043.png]]

![[Pasted image 20231002113332.png]]
![[Pasted image 20231002113939.png]]
![[Pasted image 20231002114006.png]]

```ad-note
title: notes
collapse: close
~~~json
{
  "steps": {
    "validateRequest": {
      "id": "validateRequest",
      "steps": [
        {
          "impl": "step.MsgTypeAuthCheckStep"
        },
        {
          "impl": "common.RecordAuthorizationStep"
        },
        {
          "when": {
            "type": "single",
            "op": "eq",
            "name": "networkType",
            "value": "CUPS"
          },
          "impl": "cup.MessageValidateStep"
        }
      ]
    },
    "process": {
      "id": "processRequest",
      "steps": [
        {
          "impl": "common.PrepareIasContextStep"
        },
        {
          "impl":"common.AuthIdempotentProcessStep"
        },
        {
          "impl": "step.DetermineTransactionCodeStep"
        },
        {
          "impl": "check.AuthSequenceCodeStep"
        }
      ]
    },
    "buildResponse": {
      "id": "buildResponse",
      "steps": [
        {
          "impl": "step.DetermineMostServerResponseStep"
        },
        {
          "impl": "step.GenerateApproveCodeStep"
        },
        {
          "impl": "step.CreateOutstandingStep"
        },
        {
          "impl": "step.InnerTransferStep"
        },
        {
          "when": {
            "type": "single",
            "op": "eq",
            "name": "innerTransferFlag",
            "value": "false"
          },
          "id": "process",
          "impl": "step.SendMessageStep"
        },
        {
          "id": "process",
          "impl": "step.SendSmsMessageStep"
        },
        {
          "id": "process",
          "impl": "step.SendInstallmentMessageStep"
        }
      ]
    },
    "handleError": {
      "id": "handleError",
      "steps": [
        {
          "impl": "step.PrepareRejectResponseCodeStep"
        },
        {
          "impl": "step.DetermineMostServerResponseStep"
        },
        {
          "impl": "step.CreateOutstandingStep"
        }
      ]
    },
    "logResultInNewTxn": {
      "id": "logResultInNewTxn",
      "steps": [
        {
          "impl": "common.RecordAuthorizationStep"
        }
      ]
    }
  }
}
~~~
```

> @Service("step.MsgTypeAuthCheckStep")  
> 設定 ISO MESSAGE

![[Pasted image 20231002114245.png]]

![[Pasted image 20231002120829.png]]

![[Pasted image 20231002120942.png]]


![[Pasted image 20240912154839.png]]

![[Pasted image 20231002134633.png]]

```ad-note
title: seqence code in database
collapse: open

~~~json
check.ExpiryDateAuthCheckStep:10:0:1 --Expiry Date
check.CurrencyCheckStep:20:0:1 --Currency
check.CustomerStatusAuthCheckStep:24:0:1 -- Customer Status [CUSTOMER - CSR_STS_CDE]
check.CustomerBlockAuthCheckStep:26:0:1 -- Customer Block [CUSTOMER - ATC_STS_CDE]
check.AccountStatusAuthCheckStep:30:0:1 -- Account Status [ACCOUNT - MNL_STS_CDE]
check.AccountBlockAuthCheckStep:40:0:1 -- Account Block [ACCOUNT - ATC_STS_CDE]
check.FirstUsageAuthCheckStep:50:0:1 -- First Usage
check.MccAuthCheckStep:55:0:1 -- XXX
step.ExtractTrackDataStep:60:0:1 -- XXX
check.CvvAuthCheckStep:70:0:1 -- XXX
check.CvvRetriesAuthCheckStep:80:0:1 -- XXX
vis.Cvv2AuthCheckStep:90:0:1 -- XXX
check.Cvv2RetriesAuthCheckStep:100:0:1 -- XXX
check.PinAuthCheckStep:110:0:1 -- XXX
check.PinRetriesAuthCheckStep:120:0:1 -- XXX
check.ApplicationLimitAuthCheckStep:130:0:1 -- XXX
step.DetermineAccountCreditConfigStep:140:0:1 -- XXX
check.CreditLimitAuthCheckStep:150:0:1 -- Credit Limit
common.UpdateAccountCreditDataStep:160:0:1 -- Update account_credit_data
check.PlasticStatusAuthCheckStep:165:0:1 -- status check
check.ApplicationStatusAuthCheckStep:166:0:1 -- status check
check.DetermineAccountVelocityConfigStep:168:0:1 -- XXX
check.BussCaseCheckStep:172:0:1 -- XXX
check.RiskControlCheckStep:173:0:1 -- XXX
check.UsageRateCheckStep:174:0:1 -- Velocity
common.UpdateVelocityDataStep:175:0:1 -- XXX
check.RollingExcessiveUsagePeriodStep:180:0:1 -- XXX
check.ExcessiveUsageAuthCheckStep:190:0:1 -- XXX
step.UpdateExcessiveUsageStep:200:0:1 -- XXX
~~~

```

---

## Auth Context

`MsgTypeAuthCheckStep.java`  
context.setAttribute(IasContextKeys.NETWORK_TYPE, message.getNetworkType());  
context.setAttribute(IasContextKeys.ISO_MESSAGE, message);

`common.PrepareIasContextStep`  
context.setAttribute(IasContextKeys.RECORD_AUTHORIZATION_FLAG, IasContextKeys.RECORD_AUTHORIZATION_FLAG);

`common.PrepareIasContextStep`  
context.setAttribute(IasContextKeys.APPLICATION_BO, applicationBo);  
context.setAttribute(IasContextKeys.ACCOUNT_BO, accountBo);  
context.setAttribute(IasContextKeys.SYSTEM_BO, systemBo);  
context.setAttribute(IasContextKeys.IAS_CHECK_MODE, checkMode);  
context.setAttribute(IasContextKeys.TRANSACTION_DATE, systemBo.getCurrentSystemDate());

`step.DetermineTransactionCodeStep`
context.setAttribute(IasContextKeys.DECLINE_TRANSACTION_CODE, dclTxnCde);
context.setAttribute(IasContextKeys.ACCOUNT_TRANSACTION_BO, accountTransactionBo);  
context.setAttribute(IasContextKeys.ISSUER_TRANSACTION_CODE, aprTxnCde);

`step.DetermineMostServerResponseStep`  
context.setErrorDescription(chk.getDescription());
context.setAttribute(IasContextKeys.AUTH_STATUS_CODE,AuthResponseCodeItems.athActionCode2AthStatusCode(currentActionCode));  
context.setAttribute(IasContextKeys.AUTH_RESPONSE_CODE, currentActionCode);

`step.CreateOutstandingStep`  
context.setAttribute(IasContextKeys.TXN_OUTSTANDING_AUTHORIZATION, osgAuth);

`check.BussCaseCheckStep`  
context.setAttribute(IasContextKeys.BUSS_CASE_DEFS, bussCaseDefs);

`check.RiskControlCheckStep`  
context.setAttribute(IasContextKeys.BUSS_CASES_VELOCITY_LINKS, bussCaseVelocityLinks);
context.setAttribute(IasContextKeys.SKIP_BUSS_CASES_VELOCITY_LINKS, bussCaseVelocityLinks);

`common.RecordAuthorizationStep`  
context.setAttribute(IasContextKeys.RECORD_AUTHORIZATION_FLAG, IasContextKeys.RECORD_AUTHORIZATION_FLAG);

`check.FirstUsageAuthCheckStep`  
context.setAttribute(IasContextKeys.FIRST_USAGE_IND , DATE_LOWVALUE.equals(actBo.getAccount().getDlyCtrSttDte()) ? true : false);

`check.CustomerStatusAuthCheckStep`  
context.setAttribute(IasContextKeys.ACTION_CODE_REF, actionCodeRef);

`step.CreateOutstandingStep`  
ontext.setAttribute(IasContextKeys.TXN_OUTSTANDING_AUTHORIZATION, osgAuth);

`step.DetermineMostServerResponseStep`  
context.setAttribute(IasContextKeys.AUTH_STATUS_CODE, AuthResponseCodeItems.athActionCode2AthStatusCode(currentActionCode));  
context.setAttribute(IasContextKeys.AUTH_RESPONSE_CODE, currentActionCode);  
response.setResponseCode(extCode);

## Fast/Full Check mode

`check.MccAuthCheckStep`  
商户类别检查选项 (MCC_CHK_OTN_CDE) - NOCH 默认值  
跳过商户类别检查标识 (BPS_MCC_CHK_IND) - 0 默认值

`check.ApplicationLimitAuthCheckStep`  
是否开通境外交易标志 (ADL_LGE_PRF_3_CDE)  
账户属性 (CPE_ACT_IND) ０ 个人 １ 公司 ３ 准贷记卡
主副卡标识 (MAN_SUP_IND) 1 主 0 副

## Auth Response Code

![[Pasted image 20231002152211.png]]

# IsoMessage Filter

![[Pasted image 20240426111949.png]]

Confirmed it's possible to do like you mentioned.

Other than the ISO8583 message, the data of `Application`​ is also available for the rule engine to access.

![](file:///C:/Users/w114211/AppData/Local/Temp/msohtmlclip1/01/clip_image001.png)

Besides, the option of "Is Debit Card" condition is actually like what you just said, will be determined by the internal data (`ApplicationBo`) for that purpose, so we can assume it should be supported out of the box.

![](file:///C:/Users/w114211/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

![[Pasted image 20240426112459.png]]

Filter Backend
![[Pasted image 20240426112900.png]]

# Extra

![[1.png]]

![[2.png]]

![[3.png]]

![[4.png]]

![[5.png]]

![[6.png]]

![[2023-12-13_165501.png]]
