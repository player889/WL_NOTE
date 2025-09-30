# SQL

```sql
SELECT 'DROP TABLE "' || TABLE_NAME || '" CASCADE CONSTRAINTS;'  FROM user_tables;
```


```sql
BEGIN
    FOR seq IN (SELECT sequence_name FROM user_sequences) LOOP
        EXECUTE IMMEDIATE 'DROP SEQUENCE ' || seq.sequence_name;
    END LOOP;
END;
```

```sql
BEGIN
    FOR v IN (SELECT view_name FROM user_views) LOOP
        EXECUTE IMMEDIATE 'DROP VIEW ' || v.view_name;
    END LOOP;
END;
```


# Docker command

```
podman run --name allDB -p 1000:1521 -e ORACLE_SID=allDB -e ORACLE_PWD=allDB -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata docker.io/doctorkirk/oracle-19c
```

# Create table Space

```sql
SELECT name FROM v$datafile;
```

```sql
-- 創建 tablespace（可選，用於更好的數據管理）
CREATE TABLESPACE ERD_TBS 
DATAFILE '/opt/oracle/oradata/ALLDB/erd_tbs.dbf' 
SIZE 100M AUTOEXTEND ON;
```

```sql
-- 創建用戶 CTBC_WLPS_IAS
CREATE USER CTBC_WLPS_ERD IDENTIFIED BY CTBC_WLPS_ERD
DEFAULT TABLESPACE ERD_TBS
TEMPORARY TABLESPACE TEMP;
```

```sql
-- 授予權限
GRANT CONNECT, RESOURCE TO CTBC_WLPS_ERD;
```

```sql
-- 給予 tablespace 配額
ALTER USER CTBC_WLPS_ERD QUOTA UNLIMITED ON ERD_TBS;
```

```sql
-- 给用户分配 USERS 表空间配额
ALTER USER CTBC_WLPS_ERD QUOTA UNLIMITED ON USERS;
```

```
GRANT DBA TO CTBC_WLPS_CC;
```


# Issue

````ad-missing
title: Podman cannot find container
collapse:true

```
podman machine stop Jay
```

```
podman machine start Jay
```

````
