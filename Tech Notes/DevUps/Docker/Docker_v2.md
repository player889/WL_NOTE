#docker

```ad-note
title: notes
collapse: close

`docker build -t image:tag .`
docker build -t spring-boot:docker .
docker run -d --name spring-boot-container -p 8080:8080 spring-boot:docker

===========
docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:latest

docker exec -it mysql mysql -u root -p
docker exec指令的作用為在運行的container執行指令，所以意思就是在mysql8這個container執行mysql -u root -p的指令。

pw: my-secret-pw

docker run -d -v ~/mysql-docker-data:/var/lib/mysql -it --name mysql8 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=12345 -e MYSQL_DATABASE=mydb mysql:8 --default-authentication-plugin=mysql_native_password

-d：同--detach，以背景模式執行。
-v：同--volume，掛載host目錄到container目錄，也就是host的~/mysql-docker-data會保存container /var/lib/mysql的資料。
-it：同--interactive加--tty，作用為運行container時可登入其bash操作。
--name mysql8，命名container名稱為mysql8。
-p：同--publish。作用為將container的3306 port對映到"主機(host)"的3306 port
-e：同--env，設定環境變數。MYSQL_ROOT_PASSWORD設定MySQL root帳號的登入密碼為12345，MYSQL_DATABASE設定資料庫名稱為mydb。
mysql:8：mysql為image名稱，8為image tag。所以運行的是mysql:8的image。
--default-authentication-plugin=mysql_native_password：把儲存密碼的方式改為MySQL 5的mysql_native_password，因為MySQL 8的儲存方式預設為caching_sha2_password，但一些免費的MySQL client圖形工具如Sequel Pro，Navicat等會無法連線，所以設定此參數。

docker run -d -it --name mysql-container -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb mysql:latest

docker run -d -v //c/jay/docker_volumn/data/mysql:/var/lib/mysql -it --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb mysql:latest

docker run -it
 --name mssql_2019_2
 -v //d/DockerStorage/mssql_2019:/var/opt/mssql/data2v
 -p 1433:1433
 -e ACCEPT_EULA=Y
 -e SA_PASSWORD=[密碼]
 -e MSSQL_BACKUP_DIR=/var/opt/mssql/data2
 -e MSSQL_DATA_DIR=/var/opt/mssql/data2
 -e MSSQL_LOG_DIR=/var/opt/mssql/data2
 -e MSSQL_ERROR_LOG_FILE=/var/opt/mssql/data2
 -d mcr.microsoft.com/mssql/server

docker run -it -e ACCEPT_EULA=Y -e SA_PASSWORD=TESTtest1234 -p 9797:1433 -v //c/temp/DockerStorage:/var/opt/mssql/data -d mcr.microsoft.com/mssql/server




```

```ad-tip
title: Docker Oracle 19c - command
collapse: close

[Oracle 19c docker image Ref.](https://registry.hub.docker.com/r/doctorkirk/oracle-19c)

ORACLE_CHARACTERSET = AL32UTF8
~~~docker
docker run --name oracle-19c-ias-local -p 1521:1521 -e ORACLE_SID=iasLocal -e ORACLE_PWD=iasLocal -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/Users/w114211/docker/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
~~~

~~~docker
docker run --name lab -p 1520:1521 -e ORACLE_SID=LAB -e ORACLE_PWD=pwd -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/Users/w114211/docker/lab:/opt/oracle/oradata doctorkirk/oracle-19c
~~~

```

```ad-tip
title: [Grant Database Access to user](https://www.oracletutorial.com/oracle-administration/oracle-grant-all-privileges-to-a-user/)
collapse: close
~~~SQL
CREATE USER ias IDENTIFIED BY ias;
GRANT ALL PRIVILEGES TO ias;
SELECT user FROM dual;
~~~
- sqlplus sys/<your password>@//localhost:1521/<your SID> as sysdba [Ref.](https://lufor129.medium.com/docker-%E4%B8%89-%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Cvolume-net-5f323965486)


~~~sql
sqlplus sys/lab@//localhost:1521/LAB as sysdba
~~~


```

````ad-important
title: bind: Only one usage of each socket address (protocol/network address/port) is normally permitted.
collapse: close
Find the usage of port from cmd command, Ex find port: 1521
netstat -aon|findstr "1521"
![[Pasted image 20230301095051.png]]
Kill the PID from taks manager
taskkill /pid <PID> ==> need to test
![[Pasted image 20230301101600.png]]

```ad-important
title: Change oracle Charset (亂碼)(scrambled text/garble)
collapse: close
1. set env. variable
   ![[Pasted image 20230301112324.png|500]]
2. Check with command
   `select userenv('language') from dual;`
   `select * from V$NLS_PARAMETERS;`
3. Run with command with `sysdba`
   ~~~sql
   SQL> shutdown immediate;
   SQL> startup mount;
   SQL> alter system enable restricted session;
   SQL> alter system set job_queue_processes=0;
   SQL> alter database open;
   SQL> alter database character set internal_use AL32UTF8;
   --或者  ALTER DATABASE character set INTERNAL_USE ZHS16GBK;
   SQL> shutdown immediate;
   SQL> startup;
   SQL> alter system disable restricted session;
   ~~~
4. Check with command
   `select userenv('language') from dual;`
````

```ad-info
title: oracle docker example / Create Database User [Ref.](https://registry.hub.docker.com/r/doctorkirk/oracle-19c)
collapse: close


================================================
docker pull doctorkirk/oracle-19c
mkdir -p /your/custom/path/oracle-19c/oradata

cd /your/custom/path/

sudo chown -R 54321:54321 oracle-19c/

Run the Container

docker run --name oracle-19c \
-p 1521:1521 \
-e ORACLE_SID=[ORACLE_SID] \
-e ORACLE_PWD=[ORACLE_PASSWORD] \
-e ORACLE_CHARACTERSET=[CHARSET] \
-v /your/custom/path/oracle-19c/oradata/:/opt/oracle/oradata \
doctorkirk/oracle-19c

================================================

docker run --name oracle-19c -p 1521:1521 -e ORACLE_SID=ctbcdb -e ORACLE_PWD=ctbcdb -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/Users/w114211/docker/oracle-19c:/opt/oracle/oradata doctorkirk/oracle-19c

Hostname: localhost
Port: 1521
Service Name: ctbcdb
Username: sys
Password: ctbcdb
Role: AS SYSDBA

CREATE USER player889 IDENTIFIED BY hsiao889;
GRANT CREATE TABLE TO player889;
GRANT CREATE SESSION TO player889;
ALTER USER player889 quota 100M on USERS;

CREATE TABLE test_db (
    name  VARCHAR(255),
    age   INT
);

insert into test_db values ('John', 5);

login as sysdba
sqlplus sys/uBNI8Z@127.0.0.1:1521/lab as sysdba

```

````ad-note
title: Spring Boot with Docker
collapse: close

```ad-tip
title: Image
collapse: close
~~~docker
docker build -t spring-boot-image:version .
~~~
- `-t` create image command
- `spring-boot-image` docker image name
- `version` docker image tag nam
```

```ad-tip
title: Container
collapse: close
~~~docker
docker run -d --name spring-boot-container-name -p 8080:8080 spring-boot-image:version
~~~
- `-d` run jar in background
- `spring-boot-container-name` docker container name
- `-p` match port
- `spring-boot-image` docker image name
- `version` image image tag name
```

```ad-tip
title: Database(MySql)
collapse: close
~~~docker
docker pull mysql
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb mysql:latest
~~~
- `-d` run in background
- `--name` container name
- `-p` running port
- `-e` environment
	- `MYSQL_DATABASE`,`MYSQL_USER`, `MYSQL_PASSWORD`, etc.
- `mysql:latest` image name and tag
```

```ad-tip
title: Volumn
collapse: close
~~~docker
docker run -d -v //c/jay/docker_volumn/data/mysql:/var/lib/mysql -it --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb mysql:latest
~~~
- `-v` volume
- `//c/jay/docker_volumn/data/mysql` windows location from c driver
- `/var/lib/mysql` default mysql volume
```

備註:
1. host機器的目錄路徑必須為全路徑(準確的說需要以/或~/開始的路徑)
2. 如果host機器上的目錄不存在，docker會自動創建該目錄
3. 如果container中的目錄不存在，docker會自動創建該目錄
4. 如果container中的目錄已經有內容，那麼docker會使用host上的目錄將其覆蓋掉

````

# Kafka Compose

````ad-summary
title: Docker-compose with Docker
collapse: open

1. check docker-compose version
	~~~powershell
	docker-compose -v
	~~~
2. Go the the directory of the kafka compose (yaml) file
	~~~powershell
	C:\Users\feng\Kafka\kraft>ls
	docker-compose.yml
	~~~
	```ad-important
	title: docker-compose.yml
	collapse: close
	~~~yaml
	version: "2"
	services:
	zookeeper:
		image: docker.io/bitnami/zookeeper:3.8
		container_name: optimus_zookeeper
		ports:
		  - "2181:2181"
		volumes:
		  - "C:/jay/docker_volumn/data/zookeeper_data:/bitnami"
		environment:
		  - ALLOW_ANONYMOUS_LOGIN=yes
		restart: unless-stopped
	kafka:
		image: docker.io/bitnami/kafka:3.4
		container_name: optimus_kafka
		ports:
		  - "9092:9092"
		volumes:
		  - "C:/jay/docker_volumn/data/kafka_data:/bitnami"
		environment:
		  - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092
		  - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://127.0.0.1:9092
		  - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
		  - ALLOW_PLAINTEXT_LISTENER=yes
		depends_on:
		  - zookeeper
		restart: unless-stopped
	~~~
	```
3. Execute Compose Kafka docker file
   ~~~powershell
   C:\Users\feng\Kafka\kraft>docker-compose up -d
   ~~~

   or

   ~~~powershell
   docker-compose -p myapp up -d
   ~~~

   use the `docker-compose` command with the `-p` or `--project-name` option followed by the desired name.This will start the containers defined in your docker-compose.yml file with the project name "myapp". You can replace "myapp" with any name you prefer. The name will be visible in the Docker Desktop UI.

   or

	~~~powershell
	docker-compose -f docker-compose.yaml up -d
	~~~

   `-f` specific docker file

	![[2023-03-06_144825.png|400]]
	![[2023-03-06_144839.png|400]]
	![[2023-03-06_145951.png|800]]

4. check docker status
   ~~~powershell
   docker ps
   ~~~
   ![[Pasted image 20230306155845.png]]

- 進入kafka容器
  ```powershell
  docker exec -it kafka /bin/bash
  docker exec -it oracle /bin/bash
  ```
  names = kafka

  ![[Pasted image 20230306155059.png]]

- [Docker Compose 指令](https://www.osslab.tw/books/docker/page/docker-compose-%E6%8C%87%E4%BB%A4)

````

```ad-note
title: 常用指令

~~~
cp source target
~~~

Localhost –> Container
~~~
docker cp <path of local host> containerid:<path of container>
~~~
Container –> Localhost
~~~
docker cp containerid:<path of container> <path of local host>
~~~

複製所有資料夾的檔案至container的資料底下
~~~
docker cp C:\debezium\. optimus_kafka:/opt/bitnami/kafka/libs/
~~~

刪除資料夾
~~~
rm -rf mkdir
~~~


查詢檔案
~~~
ls -d xxx*
ls -d *xxx*
~~~

```

````ad-tip
title: Docker Kafka command

```ad-quote
title: Enter Kafka Container

~~~
docker exec -it kafka /bin/bash
~~~

```



```ad-quote
title: list of topic in kafka
~~~cmd
cd /usr/bin
kafka-topics.sh --list --bootstrap-server localhost:9092
~~~
![[Pasted image 20230516152906.png]]


```





```ad-quote
title: get all message from kafka topic

~~~
cd /usr/bin
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic testTopicOUT --from-beginning
~~~
![[Pasted image 20230518120530.png]]
```


```ad-attention
title: get all message from one command in win env.
~~~
docker exec -it kafka kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic AccountStatusEventOUT --from-beginning
~~~
```


````

## Docker Desktop 啟動失敗：Docker failed to initialize

1. 删除 C:\Users\Administrator\AppData\Roaming\Docker 文件夹。
2. 删除 C:\Users\Administrator\AppData\Roaming\Docker Desktop 文件夹。  
   [Ref](https://www.cnblogs.com/cnwcl/p/15187241.html)

---

# Install Step

## download oracle images

https://registry.hub.docker.com/r/doctorkirk/oracle-19c

> docker image ls

### backup images

```shell
docker save -o C:/jay/docker/backup/images/oracle-19c.tar doctorkirk/oracle-19c
```

```shell
docker save -o C:/jay/docker/backup/images/kafka.tar bitnami/kafka:3.4
```

```shell
docker save -o C:/jay/docker/backup/images/zookeeper.tar bitnami/zookeeper:3.8
```

```shell
docker save -o C:/jay/docker/backup/images/minio.tar minio/minio
```

### load images

```shell
docker load -i C:\jay\docker\backup\images\oracle-19c.tar
```

<span style="color:red;">`rir:ArrowRight`</span> Before create container makes sure the image name and tag match the name of `doctorkirk/oracle-19c:latest`

rename the images by following command

```shell
docker image list
```

```shell
docker image tag  85e9d14710b9 doctorkirk/oracle-19c:latest
```

<span style="color:red;">`fas:ExclamationCircle`</span> `Error response from daemon: No command specified.`

```shell
 docker ps --no-trunc
```

doctorkirk/oracle-19c command

```shell
"/bin/sh -c 'exec $ORACLE_BASE/$RUN_FILE'"
```

## Create container

### IAS

#### DD for CTBC

```
docker run --name DD_CTBC -p 1531:1521 -e ORACLE_SID=newIasLocal -e ORACLE_PWD=newIasLocal -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

#### IAS

```shell
docker run --name IAS -p 1521:1521 -e ORACLE_SID=iasLocal -e ORACLE_PWD=iasLocal -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

#### Clean IAS

```
docker run --name CLEAN_WLPS_IAS -p 1533:1521 -e ORACLE_SID=cleanIAS -e ORACLE_PWD=cleanIAS -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

```docker
docker run --name ias_v2 -p 1527:1521 -e ORACLE_SID=iasLocal2 -e ORACLE_PWD=iasLocal2 -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

```
docker run --name ias_v2 -p 1527:1521 -e ORACLE_SID=iasLocal2 -e ORACLE_PWD=iasLocal2 -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

#### TEST

```
docker run --name iasTestDB -p 1534:1521 -e ORACLE_SID=iasLocalTest -e ORACLE_PWD=iasLocalTest -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

---

Archive

```
docker run --name ias_v3 -p 1528:1521 -e ORACLE_SID=iasLocal3 -e ORACLE_PWD=iasLocal3 -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

```
docker run --name WLPS_CTBC_IAS -p 1529:1521 -e ORACLE_SID=WLPS_CTBC_IAS -e ORACLE_PWD=WLPS_CTBC_IAS -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

```
docker run --name newIAS -p 1531:1521 -e ORACLE_SID=newIasLocal -e ORACLE_PWD=newIasLocal -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

### IBO

```shell
docker run --name ibo -p 1522:1521 -e ORACLE_SID=ibo -e ORACLE_PWD=ibo -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

### CC

```shell
docker run --name cc -p 1523:1521 -e ORACLE_SID=cc -e ORACLE_PWD=cc -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

### ABO

```shell
docker run --name abo -p 1524:1521 -e ORACLE_SID=abo -e ORACLE_PWD=abo -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

### wlps_cc

```shell
docker run --name wlps_cc -p 1525:1521 -e ORACLE_SID=wlpsCC -e ORACLE_PWD=cc -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

### wlps_abo_extra

```shell
docker run --name wlps_abo_extra -p 1526:1521 -e ORACLE_SID=wlpsABOextra -e ORACLE_PWD=abo -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

### ctbc-wlps-idm

```
docker run --name ctbc-wlps-idm -p 1530:1521 -e ORACLE_SID=ctbcIDM -e ORACLE_PWD=ctbcIDM -e ORACLE_CHARACTERSET=AL32UTF8 -v C:/jay/docker/volumn/oracle-19c/oradata:/opt/oracle/oradata doctorkirk/oracle-19c
```

## Create database user

```sql
CREATE USER ias IDENTIFIED BY IASUSER;
GRANT ALL PRIVILEGES TO IASUSER;
SELECT * FROM all_users WHERE USERNAME = 'IASUSER'
```

### Connect with sys

In the "New/Select Database Connection" window:

- **Connection Name:** Give it a name of your choice.
- **Username:** SYS (for administrative tasks)
- **Password:** iasLocal (or the password you specified with `-e ORACLE_PWD=iasLocal`)
- **Role:** SYSDBA
- **Hostname:** localhost
- **Port:** 1521
- **SID:** iasLocal

### Copy Database

![[Pasted image 20231006141505.png]]

## Kafka

1. Check docker compose version `docker-compose -v`
2. Go the path of the compose file
3. Execute the compose file `docker-compose -p XXXX up -d` (without file extension)

## remove container

```shell
docker rename CONTAINER_ID NEW_NAME
```

# MINIO

```
docker run -p 29000:29000 -p 9001:9001 --name minio -d --restart=always -e MINIO_ACCESS_KEY=minioadmin -e MINIO_SECRET_KEY=minioadmin -v C:/jay/docker/volumn/minio:/data -v C:/jay/docker/volumn/minio/config:/root/.minio minio/minio server /data --console-address ":9001" --address ":29000"
```

## S3 API Requests must be made to API port

Need to use the correct port, in this case is 29000

```yaml
minio:
  url: http://localhost:29000
  accessKey: minioadmin
  secretKey: minioadmin
  bucketName: fcs-bucket
```

# Cannot @Autowired other module @FeignClient - XXXXXXRestClient

![[Pasted image 20240314173453.png]]

![[Pasted image 20240314173420.png]]

# Oracle datetime chinese -> america

ALTER SESSION SET NLS_DATE_FORMAT = 'DD-MON-YYYY';  
ALTER SESSION SET NLS_TERRITORY = 'AMERICA';  
ALTER SESSION SET NLS_LANGUAGE = 'AMERICAN';  
ALTER SESSION SET NLS_DATE_LANGUAGE = 'AMERICAN';
