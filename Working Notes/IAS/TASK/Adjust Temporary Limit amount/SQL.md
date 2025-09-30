update cps_system set SYS_DTE = '2023-06-16'  
update trigger_table set AVN_DTE = DATE '2023-06-17' where TGR_IDN_NBR = '310'

#1

```SQL
clear screen;
UPDATE account_financial_meta
SET
    temp_credit_limit = 110,
    temp_credit_limit_start_date = DATE '2023-06-26',
    temp_credit_limit_end_date = DATE '2023-06-27'
WHERE
    account_id = '1D5B9A783B0040A0A87C836678274998';

UPDATE account_financial_meta SET last_modified_date = last_modified_date + interval '1' second WHERE account_id = '1D5B9A783B0040A0A87C836678274998';

UPDATE customer_financial_meta
SET
    temp_credit_limit = 111,
    temp_credit_limit_start_date = DATE '2023-06-26',
    temp_credit_limit_end_date = DATE '2023-06-27'
WHERE
    customer_id = '4875F1B04B934D7785437754E9B559FC';

UPDATE customer_financial_meta SET last_modified_date = last_modified_date + interval '1' second WHERE customer_id = '4875F1B04B934D7785437754E9B559FC';
```

#2

```SQL
clear screen;
UPDATE account_financial_meta
SET
    temp_credit_limit = 210,
    temp_credit_limit_start_date = DATE '2023-06-26',
    temp_credit_limit_end_date = DATE '2023-06-27'
WHERE
    account_id = 'BAFB786DC16549E58C9755526AB66F30';

UPDATE account_financial_meta SET last_modified_date = last_modified_date + interval '1' second WHERE account_id = 'BAFB786DC16549E58C9755526AB66F30';

UPDATE customer_financial_meta
SET
    temp_credit_limit = 211,
    temp_credit_limit_start_date = DATE '2023-06-26',
    temp_credit_limit_end_date = DATE '2023-06-27'
WHERE
    customer_id = 'DB5D52A4C526321BE055020C29ED0C0A';

UPDATE customer_financial_meta SET last_modified_date = last_modified_date + interval '1' second WHERE customer_id = 'DB5D52A4C526321BE055020C29ED0C0A';
```

```SQL
delete  batch_execute_detail;
delete batch_execute_record;
delete batch_job_execution;
delete batch_job_execution_context;
delete batch_job_execution_params;
delete batch_job_instance;
delete batch_step_execution;
delete batch_step_execution_context;
```

```SQL
SELECT
    app.pan_txt,
    acc.cry_cde,
    app.end_dte,
    app.stt_dte,
    acd.cdt_def_idn_cde,
    acd.cdt_lmt_amt, --信用限额
    acd.ave_bal_amt, --可用余额
    acd.tot_crd_lmt_amt, --总信用限额
    acd.crd_usd_lmt_amt --已使用信用限额
FROM
    account              acc
    LEFT JOIN application          app ON acc.act_idn_sky = app.act_idn_sky
    LEFT JOIN account_credit_data  acd ON acd.act_idn_sky = acc.act_idn_sky
WHERE
    acc.act_idn_sky = 'ACT5A5FE006EE684';
```

```
1D5B9A78-3B00-40A0-A87C-836678274998 - ACT5A5FE006EE684

BAFB786D-C165-49E5-8C97-55526AB66F30 - ACT8D96129AFF5F5

select * from account_credit_data where act_idn_sky = 'ACT5A5FE006EE684'; -- ACCOUNT
select * from account_credit_data where act_idn_sky = 'ACTACAB81781222E'; -- CUSTOMER

select * from account_credit_data where act_idn_sky = 'ACT8D96129AFF5F5'; --ACCOUNT
select * from account_credit_data where act_idn_sky = 'ACT8BC26EE90F949'; --CUSTOMER

```

!!Base

```SQL
select customer_id from account where id = 'BAFB786DC16549E58C9755526AB66F30'
select ID, card_token_id from card where account_id  ='BAFB786DC16549E58C9755526AB66F30'
select card_number from card_token where id = '4583D66B97FE44FFB821904F4D4B8FE5'

select * from customer_financial_meta where  customer_id = 'DB5D52A4C526321BE055020C29ED0C0A'
```
