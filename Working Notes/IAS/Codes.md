## common.PrepareIasContextStep

```sql
SELECT CHK_MOD_IND FROM INSTITUTION i
```

1 == CHK_MOD_IND <span style="color:red;">`rir:ArrowRight`</span> fullCheck
#fullcheck

```java
context.setAttribute(IasContextKeys.APPLICATION_BO, applicationBo);
context.setAttribute(IasContextKeys.ACCOUNT_BO, accountBo);
context.setAttribute(IasContextKeys.SYSTEM_BO, systemBo);
context.setAttribute(IasContextKeys.IAS_CHECK_MODE, checkMode);
context.setAttribute(IasContextKeys.TRANSACTION_DATE, systemBo.getCurrentSystemDate());
```

## common.PrepareIasContextStep
