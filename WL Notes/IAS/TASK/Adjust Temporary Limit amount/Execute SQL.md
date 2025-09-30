```SQL
DELETE FROM trigger_table;
```

```SQL
SELECT
    tgr_idn_nbr,
    seq_nbr_sky,
    avn_dte,
    key_dta_txt
FROM
    trigger_table order by tgr_idn_nbr asc;



--88800
select * from trigger_table where key_dta_txt like 'ACT5A5FE006EE684-CC01-ACCOUNT-%';

--delete trigger_table where tgr_idn_nbr = '330';
```

```SQL
CLEAR SCREEN;

UPDATE account_financial_meta
SET
    temp_credit_limit = 11,
    permanent_credit_limit = 22,
    temp_credit_limit_start_date = DATE '2023-06-26',
    temp_credit_limit_end_date = DATE '2023-06-27'
WHERE
    account_id = '1D5B9A783B0040A0A87C836678274998';

UPDATE account_financial_meta
SET
    last_modified_date = last_modified_date + INTERVAL '1' SECOND
WHERE
    account_id = '1D5B9A783B0040A0A87C836678274998';
```

```SQL
clear screen;
UPDATE account_financial_meta
SET
    temp_credit_limit = 0,
    temp_credit_limit_start_date = DATE '2023-06-26',
    temp_credit_limit_end_date = DATE '2023-06-27'
WHERE
    account_id = '1D5B9A783B0040A0A87C836678274998';

UPDATE account_financial_meta SET last_modified_date = last_modified_date + interval '1' second WHERE account_id = '1D5B9A783B0040A0A87C836678274998';
```

```SQL
clear screen;
UPDATE account_financial_meta
SET
    permanent_credit_limit = 55
WHERE
    account_id = '1D5B9A783B0040A0A87C836678274998';

UPDATE account_financial_meta SET last_modified_date = last_modified_date + interval '1' second WHERE account_id = '1D5B9A783B0040A0A87C836678274998';
```

```SQL
clear screen;
UPDATE account_financial_meta
SET
    temp_credit_limit = 11111,
    temp_credit_limit_start_date = DATE '2023-06-26',
    temp_credit_limit_end_date = DATE '2023-06-27'
WHERE
    account_id = '1D5B9A783B0040A0A87C836678274998';

UPDATE account_financial_meta SET last_modified_date = last_modified_date + interval '1' second WHERE account_id = '1D5B9A783B0040A0A87C836678274998';
```

```SQL
--IBO
--5488880000000003
SELECT
    ct.card_number,
    a.id
FROM
    account     a
    LEFT JOIN card        c ON a.id = c.account_id
    LEFT JOIN card_token  ct ON c.card_token_id = ct.id
WHERE
    c.account_id = '1D5B9A783B0040A0A87C836678274998';
```
