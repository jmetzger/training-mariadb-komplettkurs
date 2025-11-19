# mariadb-dump

## Dumping (best option) without active binary log 

```
mariadb-dump --all-databases --single-transaction > /usr/src/all-databases.sql
# if you want to include procedures use --routines 
# with event - scheduled tasks 
mariadb-dump --all-databases --single-transaction --routines --events > /usr/src/all-databases.sql
```

### Windows-Version 

```
mariadb-dump -uroot -p --all-databases --single-transaction --routines --events > C:\Users\Administrator\Desktop\all-databases.sql
```

## Useful options for PIT 

```
# —quick not needed, because included in —opt which is enabled by default 

# on local systems using socket, there are no huge benefits concerning --compress
# when you dump over the network use it for sure 
mariadb-dump --all-databases --single-transaction --routines --events  --master-data=2 --flush-logs  > /usr/src/all-databases.sql;
```

## With PIT_Recovery you can use --delete-master-logs 

  * All logs before flushing will be deleted 
  
```
mariadb-dump --all-databases --single-transaction --gtid --master-data=2 --routines --events --flush-logs --delete-master-logs > /usr/src/all-databases.sql;
```

```
# from mariadb 11.4
mariadb-dump --all-databases --single-transaction --gtid --master-data=2 --routines --events --flush-logs --delete-master-logs > /usr/src/all-databases.sql;
```

## Flush binary logs from mysql 

```
mariadb -e "PURGE BINARY LOGS BEFORE '2013-04-22 09:55:22'";

```

## Version with zipping 

```
mariadb —-all-databases —-single-transaction —-gtid —-master-data=2 —-routines 
--events —-flush-logs --compress | gzip > /usr/src/all-databases.sql.gz  
```

## Performance Test mysqldump (1.7 Million rows in contributions) 

```
date; mariadb-dump --all-databases --single-transaction --gtid --master-data=2 --routines --events --flush-logs --compress > /usr/src/all-databases.sql; date
Mi 20. Jan 09:40:44 CET 2021
Mi 20. Jan 09:41:55 CET 2021 
```

## Seperated sql-structure files and data-txt files including master-data for a specific database 

```
 # backups needs to be writeable for mysql 
 mkdir /backups
 chmod 777 /backups
 chown mysql:mysql /backups
 mariadb-dump --tab=/backups contributions
 mariadb-dump --tab=/backups --master-data=2 contributions
 mariadb-dump --tab=/backups --master-data=2 contributions > /backups/master-data.tx
```

## Create new database base on sakila database 

```
cd /usr/src
mariadb-dump sakila > sakila-all.sql 
echo "create database mynewdb" | mariadb
mariadb mynewdb < sakila-all.sql 
```
