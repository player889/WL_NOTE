
```sql
sqlplus /nolog
CONNECT sys/pwd AS SYSDBA
alter system set db_recovery_file_dest_size = 10G;
alter system set db_recovery_file_dest = '/opt/oracle/oradata/recovery_area' scope=spfile;
shutdown immediate
exit;
sqlplus / as sysdba
startup mount
alter database archivelog;
alter database open;
-- Should now "Database log mode: Archive Mode"
archive log list

exit;
```

docker exec -it lab /bin/bash

sqlplus / as sysdba
sqlplus sys/pwd@//localhost:1521/LAB as sysdba

https://support.huaweicloud.com/intl/en-us/usermanual-roma/fdi-ug-190624017.html



create role dbzuser ;
grant create session,  execute_catalog_role,  select any transaction, flashback any table, select any table, lock any table,  select any dictionary to dbzuser ;
grant select on SYSTEM.LOGMNR_COL$ to dbzuser ;
grant select on SYSTEM.LOGMNR_OBJ$ to dbzuser ;
grant select on SYSTEM.LOGMNR_USER$ to dbzuser ;
grant select on SYSTEM.LOGMNR_UID$ to dbzuser ;
grant select on V_$DATABASE to dbzuser ;
grant select_catalog_role to dbzuser ;


create user loguser identified by logpw default tablespace users;
grant dbzuser  to loguser;
alter user loguser quota unlimited on users;

GRANT ALL PRIVILEGES TO loguser;

```text
alter database force logging;
alter database add supplemental log data;
select force_logging, supplemental_log_data_min from v$database;
YES | YES

```

Caused by: oracle.jdbc.OracleDatabaseException: ORA-01031: insufficient privileges
GRANT ALL PRIVILEGES TO loguser;

CREATE USER labuser IDENTIFIED BY labpwd;  
ALTER USER labuser quota 100M on USERS;
GRANT ALL PRIVILEGES TO labuser;

ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;

ALTER DATABASE DROP SUPPLEMENTAL LOG DATA;

ALTER TABLE customer ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

SELECT * FROM dba_users order by username

![[Pasted image 20230508142352.png|300]]

![[Pasted image 20230508142554.png|500]]

![[Pasted image 20230508145039.png|800]]

---
`fas:Exclamation`No Soultion - start over oracle container
Caused by: oracle.jdbc.OracleDatabaseException: ORA-01281: SCN range specified is invalid
ORA-06512: at "SYS.DBMS_LOGMNR", line 72
ORA-06512: at line 1

java.sql.SQLException: ORA-01281: SCN range specified is invalid