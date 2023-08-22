# mysqldump 

## Dumping (best option) without active binary log 

```
mysqldump --all-databases --single-transaction > /usr/src/all-databases.sql
# if you want to include procedures use --routines 
# with event - scheduled tasks 
mysqldump --all-databases --single-transaction --routines --events > /usr/src/all-databases.sql
```

## Useful options for PIT 

```
# —quick not needed, because included in —opt which is enabled by default 

# on local systems using socket, there are no huge benefits concerning --compress
# when you dump over the network use it for sure 
mysqldump --all-databases --single-transaction --gtid --master-data=2 --routines --events --flush-logs  > /usr/src/all-databases.sql;
```

## With PIT_Recovery you can use --delete-master-logs 

  * All logs before flushing will be deleted 
  
```
mysqldump --all-databases --single-transaction --gtid --master-data=2 --routines --events --flush-logs --delete-master-logs > /usr/src/all-databases.sql;
```

## Flush binary logs from mysql 

```
mysql -e "PURGE BINARY LOGS BEFORE '2013-04-22 09:55:22'";

```

## Version with zipping 

```
mysqldump —-all-databases —-single-transaction —-gtid —-master-data=2 —-routines 
--events —-flush-logs --compress | gzip > /usr/src/all-databases.sql.gz  
```

## Performance Test mysqldump (1.7 Million rows in contributions) 

```
date; mysqldump --all-databases --single-transaction --gtid --master-data=2 --routines --events --flush-logs --compress > /usr/src/all-databases.sql; date
Mi 20. Jan 09:40:44 CET 2021
Mi 20. Jan 09:41:55 CET 2021 
```

## Seperated sql-structure files and data-txt files including master-data for a specific database 

```
 # backups needs to be writeable for mysql 
 mkdir /backups
 chmod 777 /backups
 chown mysql:mysql /backups
 mysqldump --tab=/backups contributions
 mysqldump --tab=/backups --master-data=2 contributions
 mysqldump --tab=/backups --master-data=2 contributions > /backups/master-data.tx
```

## Create new database base on sakila database 

```
cd /usr/src
mysqldump sakila > sakila-all.sql 
echo "create database mynewdb" | mysql
mysql mynewdb < sakila-all.sql 
```
