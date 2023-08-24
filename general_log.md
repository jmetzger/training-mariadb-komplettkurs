# general log 

## Exercise 

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

## Disabled / Enable general_log during runtime 

```
# if general_log is activated disable like so
mysql
set global general_log = 0

# activate if not activated
set global general_log = 1

# this is not persistent will be reset to default or setting my.cnf.d/server.cnf - config
```
