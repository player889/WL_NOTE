```SQL
--IAS DATA SYNC LOG TABLE
Table DATA_SYNC_CHANGE_LOG created.
Table DATA_SYNC_LOG created.
Table DATA_SYNC_OUTBOX created.
Table DATA_SYNC_REQUEST created.
```

```
AF6CE5C1F6170C62E055020C29ED0C0A	BANK OPTIMUS
F61DDF5A6CCF4CEFB4EE2CEB9695CA9C	SYSTEM DEFAULT
BC814F2523DF42E8B8B2D891D8B9BB10	Test Bank 2
E58F12006AC24385B68C9F1DA6EA3F51	Bank Jakarta
D73427A4ABE748E597D64F1892B3C827	malaysia bank
AF6DC8F9C4516825E055020C29ED0C0A	WLPS Bank
E652DD9F3EED49CE8408BD87F157EB50	CTBC Bank Co. Ltd
A0BB91CFC8564A398330A68D4DE959CA	CTBC Bank Co. Ltd
1C5F51E0D6D44288BF0543A6489D48A7	CTBC Bank Co. Ltd
F94F65BCCAC04913930C5BB863B18A6F	Test Bank
```

```SQL
SELECT
    a.account_number,
    a.organization_id,
    ct.card_number
FROM
    card        c
    LEFT JOIN account     a ON c.account_id = a.id
    LEFT JOIN card_token  ct ON ct.id = c.card_token_id
WHERE
    c.card_relation = 'S';
```

```SQL
SELECT
    c.card_token_id,
    ct.card_number
FROM
    card        c
    LEFT JOIN card_token  ct ON c.card_token_id = ct.id
WHERE
    ct.card_number = '5488880000000002';
```

```SQL
SELECT
    acc.id as ACCOUNT_ID,
    c.id as CARD_ID,
    c.card_token_id,
    ct.card_number,
    c.organization_id
FROM
account acc
left join  card        c on acc.id = c.account_id
    LEFT JOIN card_token  ct ON c.card_token_id = ct.id
WHERE
    ct.card_number = '5230910000000006';
```
