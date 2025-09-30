1. 限制類交易規則和自行設定交易規則 過濾表達式中運算符合需要增加：not in
2. Authorization > Issuing Authorization Parameter > Transaction Rule 新增規則引擎數據：增加欄位，’Token Requestor ID’，’交易來源’，’卡號’
3. SDI 模組錯誤，CVC (4CSC)驗證失敗，{"success":false,"code":"99","description":"System error","data":null}
4. 與 Weiren 打通 VISA、NCCC、MasterCard simulator 端到端授權交易
5. 持卡人 ID 授權，如果未查到正確的卡片，BASE&PROJECT 需要一併更新
6. Maker Checker
7. Swagger Authorization
8. 更新 Auth Seq code --> Check item
   ![[Pasted image 20250801171258.png|400]]

```txt
vis.DetermineTransactionTypeStep:5:0:1;mcc.DetermineTransactionTypeStep:6:0:1;ncc.DetermineTransactionTypeStep:7:0:1;common.LocateOriginalOutstandingStep:10:0:1;step.AuthTransactionBaseRuleCheckStep:15:0:1;step.IntegratedDailyAndCycleDataAgingStep:20:0:1;check.IntegratedExtractTrackDataStep:25:1:1;check.cardTransactionStep:26:1:1;check.RestrictTransactionStep:27:1:1;check.ExpiryDateAuthCheckStep:30:1:1;check.MerchantOnlineTransactionStep:35:1:1;check.IntegratedPinAuthCheckStep:50:1:1;chip.ARQCVerificationAuthCheck:55:1:1;chip.ChipTvrAuthCheckStep:56:1:1;chip.ChipCvrAuthCheckStep:57:1:1;check.IntegratedCurrencyCheckStep:60:0:1;check.IntegratedCustomerStatusAuthCheckStep:62:1:1;check.IntegratedCustomerActionAuthCheckStep:64:1:1;check.IntegratedCorpCustomerStatusAuthCheckStep:65:1:1;check.IntegratedCorpCustomerActionAuthCheckStep:66:1:1;check.IntegratedSuppCustomerStatusAuthCheckStep:67:1:1;check.IntegratedSuppCustomerActionAuthCheckStep:68:1:1;check.IntegratedAccountStatusAuthCheckStep:70:1:1;check.IntegratedAccountActionAuthCheckStep:71:1:1;check.IntegratedCardStatusAuthCheckStep:75:1:1;check.IntegratedCardActionAuthCheckStep:80:1:1;check.IntegratedFirstUsageAuthCheckStep:85:0:1;check.IntegratedMerchantTransactionControlStep:86:0:1;check.IntegratedAccountLimitCheckStep:95:1:1;step.DetermineAccountCreditConfigStep:100:0:1;check.IntegratedCreditLimitAuthCheckStep:101:1:1;step.IntegratedAccountLimitUpdateStep:102:1:1;check.BussCaseCheckStep:100:0:1;check.RiskControlCheckStep:100:0:1;check.UsageRateCheckStep:100:0:1
```

4.
5.
