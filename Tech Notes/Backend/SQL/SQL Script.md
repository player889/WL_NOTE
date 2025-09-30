````ad-note
title: SQL Script
collapse: open

```ad-hint
title: Add index to selected values
collapse: close
~~~SQL
SELECT *,ROW_NUMBER() Over(order by platformCode asc) from ec_ws_car_channel WHERE 1 = 1;
~~~
```

```ad-hint
title: Select first page
collapse: close
~~~SQL
select *  from ( SELECT *,ROW_NUMBER() Over(order by platformCode asc ) as rownum FROM ec_ws_car_channel WHERE 1 = 1 ) as t1 where t1.rownum between ((1-1) *10 +1) and 1 * 10;
~~~
```

```ad-hint
title: Get Text in parentheses
collapse: close
~~~SQL
select SUBSTRING(colume from '\((.+)\)') from dual where colume = 'IRP(WI)'
~~~
```

```ad-tip
title: Drop All Tables
collapse: open
#drop
~~~SQL
SELECT 'DROP TABLE "' || TABLE_NAME || '" CASCADE CONSTRAINTS;'  FROM user_tables;
~~~

#truncat
~~~SQL
SELECT 'TRUNCATE TABLE ' || TABLE_NAME ||';'  FROM user_tables;
~~~


#drop_sequence
~~~SQL
BEGIN
    FOR seq IN (SELECT sequence_name FROM user_sequences) LOOP
        EXECUTE IMMEDIATE 'DROP SEQUENCE ' || seq.sequence_name;
    END LOOP;
END;
~~~
~~~SQL
select 'drop sequence ' || sequence_name || ';' from user_sequences;
~~~

#drop_view
~~~SQL
BEGIN
    FOR v IN (SELECT view_name FROM user_views) LOOP
        EXECUTE IMMEDIATE 'DROP VIEW ' || v.view_name;
    END LOOP;
END;
~~~
~~~
select 'drop view ' || view_name || ';' from user_views;
~~~

~~~
SELECT HIBERNATE_SEQUENCE.NEXTVAL FROM dual;
~~~


```

```ad-tip
title: execute sql fie
collapse: open
@path\script.sql;
![[Pasted image 20230717111747.png]]
```


```ad-tip
title: dump / copy database
collapse: open

![[Pasted image 20230718173053.png]]
```

```ad-note
title: Get table with exsting column name
~~~
select table_name from all_tab_columns where column_name = 'ITN_IDN_CDE' order by table_name asc
~~~
```




````

#Clone

# Auto commit with SQL developer

![[Pasted image 20230607144514.png]]

#password_expired

![[Pasted image 20240712151540.png]]

## import .dmd file (ERD)

![[Pasted image 20240729120829.png]]

## Delete sequence - IDM

SELECT sequence_name FROM all_sequences WHERE sequence_name = 'HIBERNATE_SEQUENCE';

DROP SEQUENCE IDM.hibernate_sequence;
