# PIT (Point-In-Time - Recovery - Exercise) 

## Problem coming up  

```
# Step 1 : Create full backup (assuming 24:00 o'clock)
mysqldump --all-databases --single-transaction --master-data=2 --routines --events --flush-logs --delete-master-logs > /usr/src/all-databases.sql;

# Step 2: Working on data 
mysql>use sakila; 
mysql>insert into actor (first_name,last_name) values ('john','The Rock');
mysql>insert into actor (first_name,last_name) values ('johanne','Johannson');

# Optional: Step 3: Looking into binary to see this data 
cd /var/lib/mysql 
# last binlog 
mysqlbinlog -vv mariadb-bin.000005

# Step 4: Some how a guy deletes data 
mysql>use sakila; delete from actor where actor_id > 200;
# now only 200 datasets 
mysql>use sakila; select * from actor;

```
  
## Fixing the problem 

```
# find out the last binlog 
# Simple take the last binlog 

cd /var/lib/mysql
# Find the position where the problem occured 
# and create a recover.sql - file (before apply full backup)
mysqlbinlog -vv --stop-position=857 mysqld-bin.000005 > /usr/src/recover.sql
# in case of multiple binlog like so:
# mysqlbinlog -vv --stop-position=857 mysqld-bin.000005 mysqld-bin.000096 > /usr/src/recover.sql

# Step 1: Apply full backup 
cd /usr/src/
mysql < all-databases.sql 

```

```
-- im mysql-client durch eingeben des Befehls 'mysql'
-- should be 200 or 202
use sakila; select * from actor;
```

```
# auf der Kommandozeile 
mysql < recover.sql 
```

```
-- im mysql client 
-- now it should have all actors before deletion 
use sakila; select * from actor;
```
