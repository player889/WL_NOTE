![[Pasted image 20230920173010.png]]

```SQL

--NAME : acct_WLCN_ACT404

--CARD 1
select * from application where pan_txt = '4888880000000020';
select * from account where act_idn_sky = 'ACT9C70584E45AB1';
select * from account_credit_data where act_idn_sky = 'ACT9C70584E45AB1';
--CARD 2
select * from account where act_idn_sky = 'ACT54207F52A9040';
select * from account_credit_data where act_idn_sky = 'ACT54207F52A9040';

```

<span style="color:red;">`fas:ExclamationCircle`</span> Do not honor

```SQL
-- Find out dbtCdtCde ('CRED' or 'DEBT') <== transactionProfile's DBT_CDT_CDE
SELECT DBT_CDT_CDE FROM TRANSACTION_PROFILE tp WHERE TXN_CDE = '6810';

SELECT MNL_STS_CDE FROM PLASTIC p WHERE PLA_IDN_SKY = 'PLADBBEACA704E70';
update PLASTIC SET MNL_STS_CDE = 'NORM' WHERE PLA_IDN_SKY = 'PLADBBEACA704E70';
```

![[Pasted image 20230920174914.png]]

![[Pasted image 20231117172710.png]]

![[Pasted image 20230920212433.png]]
![[2024-01-04_150801.png]]

MNL-STS-CDE  
PRDS 制卡状态：  
ACST 账户状态不允许重发卡  
APST 申请状态不允许重发卡  
CANC 卡片生成已被取消  
DEST 卡片已被客户损坏  
LSNR 丢失或失窃-未替换  
LSRP 丢失或失窃-已替换  
NEWP 需要新卡片  
NREI 未重新发卡  
PLST 卡片状态不允许重新发卡  
PROD 卡片已生成  
REIS 已重发卡  
REPL 已换卡  
RETN 卡片已被客户退还  
SCHD 已预订（外部处理）

---
![[Pasted image 20240104160833.png]]

|      |      |                  |
| ---- | ---- | ---------------- |
| ACCT | 0    | 新建               |
| ACCT | 1    | 活躍               |
| ACCT | 2    | 不活躍              |
| ACCT | 5    | 正等候凍結            |
| ACCT | 6    | 凍結               |
| ACCT | 7    | 凍結取消             |
| ACCT | 8    | 關閉               |
| ACCT | 9    | 將被刪除             |
| ACCT | A50  | 核銷帳戶（戶口標誌）       |
| ACCT | ATPD | 待激活              |
| ACCT | B70  | 不良資產剝離（本金為 0 ）   |
| ACCT | B80  | 不良資產剝離（本金小於 100  |
| ACCT | B90  | 不良資產剝離           |
| ACCT | C50  | 超過 30 天延滯        |
| ACCT | CLOP | 預銷戶              |
| ACCT | CLOS | 銷戶               |
| ACCT | DQ10 | 逾期10期            |
| ACCT | DQ11 | 逾期11期            |
| ACCT | DQ12 | 逾期12期            |
| ACCT | DQC1 | 1 cycle past due |
| ACCT | DQC2 | 逾期2期             |
| ACCT | DQC3 | 逾期3期             |
| ACCT | DQC4 | 逾期4期             |
| ACCT | DQC5 | 逾期5期             |
| ACCT | DQC6 | 逾期6期             |
| ACCT | DQC7 | 逾期7期             |
| ACCT | DQC8 | 逾期8期             |
| ACCT | DQC9 | 逾期9期             |
| ACCT | DQCO | 轉呆               |
| ACCT | DQFP | 首次逾期             |
| ACCT | DQPD | PAST DUE         |
| ACCT | DQWO | 核銷               |
| ACCT | F50  | 首期延滯             |
| ACCT | FRAU | 欺詐               |
| ACCT | H50  | 戶口超額             |
| ACCT | OLIM | 超限               |
| ACCT | P50  | 30 天內延滯          |
| ACCT | PDUE | 延滯               |
| ACCT | R10  | 長期無使用類的批量主動註銷    |
| ACCT | R20  | 市場發展需要的銀行主動註銷    |
| ACCT | R30  | 壹般類卡，持卡人提起註銷     |
| ACCT | R40  | 壹般類卡，銀行主動註銷      |
| ACCT | R50  | 高風險類卡，持卡人提起註銷    |
| ACCT | R60  | 高風險類卡，銀行主動註銷     |
| ACCT | R70  | 不良資產剝離註銷（本金為 0   |
| ACCT | R80  | 不良資產剝離註銷本金小於 100 |
| ACCT | R90  | 不良資產剝離註銷         |
| ACCT | V50  | 特殊 VIP 客戶        |
| ACCT | X50  | 戶口凍結             |
| ACCT | NORM | 正常               |
| APPL | 0    | 新開               |
| APPL | 1    | 靜止               |
| APPL | 2    | 活躍               |
| APPL | 3    | 今天更換             |
| APPL | 4    | 遺失 掛失舊卡          |
| APPL | 8    | 關閉               |
| APPL | 9    | 將被刪除             |
| APPL | U    | 卡片已升級            |
| APPL | NORM | 正常               |
| CUSB | C50  | 賬戶之壹延滯           |
| CUSB | NORM | 正常               |
| CUSB | V50  | VIP              |
| CUSC | 9    | 客戶將被刪除           |
| CUSC | NORM | 正常               |
| CUSC | V50  | VIP              |
| CUSC | 0    | 未完成              |
| CUSC | C50  | 賬戶之壹延滯           |
| CUSC | 1    | 活動               |
| PLAS | A80  | 磁道校驗錯誤           |
| PLAS | A90  | 連續輸錯密碼           |
| PLAS | ACCC | 關戶銷卡             |
| PLAS | ACTP | 待激活              |
| PLAS | B80  | 延滯且無法達到還款要求      |
| PLAS | B90  | 持卡人嫌疑惡意透支        |
| PLAS | CANC | 銷卡               |
| PLAS | D90  | 長期無使用類的批量主動註銷    |
| PLAS | E50  | 因信用風險不能再開通的卡片    |
| PLAS | FUSA | 首次使用超出限額         |
| PLAS | G50  | 劃扣止付存款           |
| PLAS | I90  | 撤銷擔保人            |
| PLAS | J50  | 拒絕交易入帳           |
| PLAS | K80  | 卡片涉嫌欺詐           |
| PLAS | K90  | 持卡人其他不良行為        |
| PLAS | L50  | 丟失卡              |
| PLAS | LOST | 丟失               |
| PLAS | M50  | 催收或信維無法聯系        |
| PLAS | N50  | 持卡人要求臨時凍結卡片      |
| PLAS | O80  | 有需核實的交易，暫時止付     |
| PLAS | O90  | 卡片資料外泄風險，需暫停使用   |
| PLAS | PINR | 密碼超次             |
| PLAS | Q60  | 賬戶接管案件盜用         |
| PLAS | Q70  | 未達卡案件盜用          |
| PLAS | Q80  | 偽冒申請             |
| PLAS | Q90  | 持卡人欺詐            |
| PLAS | R10  | 長期無使用類的批量主動註銷    |
| PLAS | R20  | 市場發展需要的銀行主動註銷    |
| PLAS | R30  | 壹般類卡，持卡人提起註銷     |
| PLAS | R40  | 壹般類卡，銀行主動註銷      |
| PLAS | R50  | 高風險類卡，持卡人提起註銷    |
| PLAS | R60  | 高風險類卡，銀行主動註銷     |
| PLAS | R70  | 不良資產剝離註銷（本金為 0）  |
| PLAS | R80  | 不良資產剝離註銷本金小於 100 |
| PLAS | R90  | 不良資產剝離註銷         |
| PLAS | S50  | 失竊卡              |
| PLAS | STOL | 被盜               |
| PLAS | STPP | 止付               |
| PLAS | T80  | 催領卡/激活/退卡外呼客戶不成功 |
| PLAS | T90  | 對帳單退信無法聯系        |
| PLAS | U50  | 其它風險狀況           |
| PLAS | W70  | 卡片被他人側錄盜用        |
| PLAS | W80  | 卡片被他人側錄盜用        |
| PLAS | W90  | 卡片不在現場/偽卡號       |
| PLAS | Y80  | 上3M標             |
| PLAS | Y90  | 審核環節非風險類銀行主動止付   |
| PLAS | Z50  | 商務卡凍結            |
| PLAS | NORM | 正常               |


![[Pasted image 20230922134443.png]]

## Transaction Log - IAS

```sql
SELECT
    *
FROM
    outstanding_authorization
WHERE
        pan_txt = '4888880000000020'
    AND apr_cde_txt = 'XXXXXXXX'
    AND apr_cde_txt IS NOT NULL
ORDER BY
    lst_upd_tme DESC;
```

## Transaction Log - ACQ

![[Pasted image 20230925120217.png]]

## Acq block

![[Pasted image 20230926111335.png]]

> **Monitor plan~~**

## External Response code to SRS
![[Pasted image 20231121173058.png]]


## Velocity Checking setting

> **Velocity Checking Rules do not used ~~`check.VelocityAuthCheckStep`~~ , Please check `check.UsageRateCheckStep` instead**

## Tolerance Amount

![[Pasted image 20230925150741.png]]

## JPY

![[Pasted image 20230926164706.png]]

If JPY need different behavior, then JPY should be the record inside this sub-table

Meaning that, when you're doing JPY transaction, the same "CREDIT_CHECK_RULE" will always used, but if you have sub-record of "JPY", then this sub-record's overlimit parameters will be used instead.\

## Velocity Check == UsageRateCheckItem ~~check.VelocityAuthCheckStep~~

Because the Velocity Checking is performed by **`UsageRateCheckItem`**

## Dual currency authorization transaction

During Credit Limit Checking in Authorization, if the account currency is not same as the transaction currency, then currency conversion will be performed using the exchange rate synchronized from `optimus-cc`

## Dual Transaction

Customer has dual with TWD and JPY account.

1. convert JPY into TWD, check if available limit has enough money.
2. If enough money, it will create a new record in table `account_credit_data` with the deducted amount. (Store the amount of spending money)
3. In the credit limit checking, it will calculated the both CC01+TWD and CC01+JPY (created one) with currency conversion rate
   Scenario
   Status : Credit limit 10,000 TWD , used 5,000 JPY (new record in account_credit_data)
   Do transaction with JPY $1000 JPY
   Credit limit : 10,000TWD to JPY $46,319 JPY
   Used limit : 5,000JPY
   Actual available amount = 41319 (46,319 - 5000)
   If passing the limit check - the new record will become $6000 JPY (5000 + 1000)
   ![[Pasted image 20230928174231.png]]
   Save or Update record in `account_credit_data` in parent account.
   ![[Pasted image 20231003160110.png]]

## Authorization of Action code at customer level

![[Pasted image 20231120100253.png]]

---

![[Pasted image 20231120140245.png]]

![[Pasted image 20231120114626.png]]

# ACTION CODE

![[Pasted image 20231120110358.png]]

1:27
ACTION CODE / STATUS CODE( FIX) / ALLOW AUTH

1:42
IBO ACTION CODE <==> IAS STATUS CODE

IBO's ACTION CODE -> Authorization Response <=> STATUS CODE

1:53
Additional rule setting for velocity checking at status code GUI (CARD/ACCOUNT)

![[Pasted image 20231120112515.png]]
# Transaction Rule
![[Pasted image 20231213093542.png]]