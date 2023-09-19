# general log 

## Exercise Version 1: Enable in config  

```
# set in configuration
# /etc/my.cnf.d/server.cnf
# under mysqld
general-log
```

```
systemctl restart mariadb
mysql
````

```
-- in mysql
select @@general_log;
show processlist;
use sakila;
select * from actor;
exit
```

```
# depending on your server-name
cd /var/lib/mysql
cat server1.log
```

## Exercise Version 1: Enable/Disable general_log during runtime 

### Step 1: 

```
# if general_log is activated disable like so
mysql
set global general_log = 1
```

### Step 2: fill with data 

```
-- in mysql
select @@general_log;
show processlist;
use sakila;
select * from actor;
exit
```

### Step 3: See what's in general_log 

```
# depending on your server-name
cd /var/lib/mysql
# servename + log 
cat server1.log
```
