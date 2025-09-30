sys_user `ris:ArrowRight` login data
sys_menu `ris:ArrowRight` menu
institution `ris:ArrowRight` institution name
auth_sequence_code `ris:ArrowRight` list of sequence code

sys_user_pass_history,"用戶表"  
sys_user,"用戶表"  
sys_demo,"模型示例表"  
sys_area,"區域表"  
sys_mdict,"多級字典表"  
sys_role_institution,"角色機構關聯表"  
sys_id_alloc,"ID 生成表"  
sys_rule_item,"系統規則表"  
sys_role_office,"角色機構關聯表"  
sys_role_menu,"角色菜單關聯表"v
sys_office,"機構表"  
sys_biz_dict,"業務字典表"  
sys_user_history,"用戶表"  
sys_menu,"菜單表"  
sys_dict,"系統字典表"  
sys_log,"日誌表"  
sys_role,"角色表"  
sys_modify_history,"修改歷史記錄表"  
sys_user_role,"用戶角色關聯表"

- When transaction processing, it will change value of `tot_amt_ptg_amt`(入帳金額合計) and `osg_ath_amt` (待入帳授權金額) in account_credit_data
- ISystemBo systemBo = systemManager.getSystemBo(dataCache);
    java.sql.Date currentSystemDate = systemBo.getCurrentSystemDate();
    String currentDate = systemManager.getSystem(dataCache).getSysDte();

Example:

```sql
SELECT
	itn_idn_cde,
	ath_seq_cde,
	cry_cde,
	ath_seq_txt
FROM
	auth_sequence_code
WHERE
	ath_seq_cde = 'DFLTDFLT6101'
	AND cry_cde = '156'
	AND itn_idn_cde = 'WLCN'
```

ath_seq_cde's value

> check.ExpiryDateAuthCheckStep:10:0:1;check.CurrencyCheckStep:20:0:1;check.CustomerStatusAuthCheckStep:24:0:1;check.CustomerBlockAuthCheckStep:26:0:1;check.AccountStatusAuthCheckStep:30:0:1;check.AccountBlockAuthCheckStep:40:0:1;check.PlasticStatusAuthCheckStep:44:0:1;check.ApplicationStatusAuthCheckStep:46:0:1;check.FirstUsageAuthCheckStep:50:0:1;check.MccAuthCheckStep:55:0:1;step.ExtractTrackDataStep:56:0:1;check.CvvAuthCheckStep:57:0:1;check.CvvRetriesAuthCheckStep:58:0:1;cup.Cvv2AuthCheckStep:60:0:1;check.Cvv2RetriesAuthCheckStep:70:0:1;check.PinAuthCheckStep:80:0:1;check.PinRetriesAuthCheckStep:90:0:1;check.ApplicationLimitAuthCheckStep:100:0:1;step.DetermineAccountCreditConfigStep:110:0:1;check.CreditLimitAuthCheckStep:120:0:1;common.UpdateAccountCreditDataStep:160:0:1;check.DetermineAccountVelocityConfigStep:170:0:1;check.BussCaseCheckStep:171:0:1;check.RiskControlCheckStep:172:0:1;check.UsageRateCheckStep:173:0:1;common.UpdateVelocityDataStep:175:0:1;check.RollingExcessiveUsagePeriodStep:180:0:1;check.ExcessiveUsageAuthCheckStep:190:0:1;step.UpdateExcessiveUsageStep:200:0:1

![[Pasted image 20230301144856.png|600]]

## Change the account status in IBO

![[Pasted image 20230921174754.png]]

## IAS Dropdown Options

1. Check web' sql file  
   ![[Pasted image 20230301162900.png|300]]![[2023-09-12_105512.png]]
2. GraphQL  
   ![[2023-10-11_161427.png|300]]
3. Example:  
   ![[Pasted image 20230301163016.png]]![[Pasted image 20231030104751.png]]
   ![[Pasted image 20231030105016.png]]
4. .graphql
   ![[Pasted image 20231026172439.png]]
   ![[Pasted image 20231027164502.png]]
   ![[Pasted image 20231030110545.png]]
5. Java - 字典工具类
    ```java
    @Service
    public class SysDictLoader implements IDictLoader { }
6. Controller For Dropdown List
    - GraphQLController
    - GraphQLService #72
        - ParameterApprovalInformationDataLoader
        - DictServiceImpl.getDict()
        - DictTextFetcher

---

```java

Consumer<ApiServiceContext> next = (ctx) -> this.runTask(ctx);
Runnable run = () -> next.accept(context);
ShardingEnv.instance().runForShard(shardKey, run);


protected void runTask(ApiServiceContext context) {
    ProcessingTask task = this.taskManager.requireProcessingTask(context.getServiceName() + "/" + context.getServiceMethod());
    context.init();
    task.execute(context);
}

```

1. Entity with `shardingRoute()` (IShardingRouteSupport)
2. Entity ==Extends== `(Abstract Enttiy) Class`
    ```java
    @Override
    public ShardingBizKey shardingRoute() {
        if (ValidationHelper.isNotEmpty(this.getActIdnSky())) {
            return ShardingBizKey.forAccount(this.getActIdnSky());
        } else {
            return ShardingBizKey.forPanTxt(this.getPanTxt());
        }
    }
    ```
    ShardingBizKey (bizType, bizKey) - bizType value - Account、Customer、Card(PanTxt)、SclScyNbrTx - bizKey: inputValue
3. _interface IShardSelector_
    ```java
    default String toShardKey(int db) {
        return "ds" + db;
    }
    ```

### Log4j2.xml

show SQL Log
![[Pasted image 20230713172112.png]]

---

### when condition with taskName within task processing

```json
{
      ...
      },{
        "when": {
          "type": "single",
          "op": "eq",
          "name": "hasDisputeProcess",
          "value": "true"
        },
        "taskName": "com.worldline.cardlite.cas.api.TransactionDisputeProcess/subDisputeTxnPosting"
      }
}
```

### when condition within task processing

```json
{
	"when": {
		"type": "single",
		"op": "eq",
		"name": "isSuspend",
		"value": "true"
	},
	"impl": "InsertSuspendData"
}
```

## expiry Date

![[2023-09-20_150643.png]]

### outstanding exception

![[2023-09-12_152412.png]]

### ACCOUNT STATUS CODE / Primary Account

```sql

```

![[Pasted image 20230913110746.png]]

### Velocity checking

First in "Business Case Velocity Link" find out which Velocity Check ID being mapped with `ycym`
![[Pasted image 20230915090054.png]]

If you create new velocity check rule, you will need new velocity check definition (Velocity Check ID), and need linking between `ycym` and `velocity check id` in `Business Case Velocity Link`

with the main group ID as `0000`

## check items

![[Pasted image 20231016173658.png]]

## Velocity Checking

- mxmCvv2RtsNbr (PCVV2/CVC2 失败最大重试次数)
- mxmCvvRtsNbr(CVV失败最大重试次数)
![[Pasted image 20230918102741.png]]

## STRUCTURE

```sql
SELECT
	app.ACT_IDN_SKY,
	app.PAN_TXT,
	s.hcy_act_1_idn_sky,
	s.hcy_act_2_idn_sky,
	s.hcy_act_3_idn_sky,
	s.hcy_act_4_idn_sky,
	s.hcy_act_5_idn_sky
FROM
	APPLICATION app
LEFT JOIN "STRUCTURE" s ON
	app.ACT_IDN_SKY = s.ACT_IDN_SKY
WHERE
	s.hcy_act_1_idn_sky = 'ACTDED25DD147153' ORDER BY APp.PAN_TXT
```

![[Pasted image 20231002174648.png]]

#公司卡

### 公司卡

#### IBO

```SQL
select * from card_token where card_number = '5100123400000095';
select ACCOUNT_ID from card where card_token_id ='695711AA7E334AB38350C96769B727EA';
SELECT CORPORATE_CUSTOMER_ID FROM account WHERE id = '8CC01F136CC04BFBBD28EE76D659EB73' ;
SELECT CUSTOMER_ID , NAME FROM CUSTOMER_CORPORATE_META ccm WHERE CUSTOMER_ID ='E09776239FCF4DC09EEE936611EED60
```

```sql
SELECT
	ct.CARD_NUMBER ,
	ccm.NAME
FROM
	ACCOUNT a
LEFT JOIN CUSTOMER_CORPORATE_META ccm ON ccm.CUSTOMER_ID = a.CORPORATE_CUSTOMER_ID
LEFT JOIN CARD c ON a.ID = c.ACCOUNT_ID
LEFT JOIN CARD_TOKEN ct ON ct.ID = c.CARD_TOKEN_ID
WHERE
	ct.card_number IN ('5100123400000095','4800982200000068','4800982200000019','4800982200000027','4800982200000035','4800982200000076','4800982200000084');
```

![[Pasted image 20231003120415.png|500]]

<span style="color:red;">`rir:ArrowRight`</span> View From Card Product

![[Pasted image 20231023144417.png]]

#### IAS

客户类型, map to column CSR_TYP_CDE [CONT/CUST(Corp)]  
法人团体标识, map to column CPE_IND [1/0]

```
SELECT CSR_TYP_CDE, COUNT(*) FROM CUSTOMER c group by CSR_TYP_CDE;
```

```sql
SELECT
	a.ITN_IDN_CDE,
	c. CSR_TYP_CDE,
	a2.PAN_TXT,
	c.CPE_IND
FROM
	ACCOUNT a
LEFT JOIN CUSTOMER c ON
	a.CSR_IDN_SKY = c.CSR_IDN_SKY
LEFT JOIN APPLICATION a2 ON
	a.ACT_IDN_SKY = a2.ACT_IDN_SKY
WHERE
	c.CSR_TYP_CDE = 'CUST' AND c.CPE_IND = '1';
```

### 附卡

#### IBO

CARD_RELATION P 主卡 S 附卡

```sql
SELECT c.CARD_RELATION FROM card c
```

![[Pasted image 20231003153121.png]]

### IAS

MAN_SUP_IND 1 主卡 0 附卡

```sql
select a.man_sup_ind, a2.PAN_TXT
from account a LEFT JOIN APPLICATION a2 ON a.ACT_IDN_SKY = a2.ACT_IDN_SKY
WHERE  a.man_sup_ind IS NOT NULL AND a.man_sup_ind = '0'
```

### Account Levle

结构级别, map to column ACT_LVL_NBR

## Fast / Full Check

![[Pasted image 20240104101059.png]]
