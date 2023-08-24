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
