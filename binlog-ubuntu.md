# Binary Logs 

## How to activitate ? 

```
nano /etc/mysql/mariadb.conf.d/zz_config.cnf
```

```
[mysqld]
log-bin
```

```
systemctl restart mariadb
cd /var/lib/mysql
# is binary log there ? mariadb-
ls -la *mysqld-bin* 
mysql
```

```
-- is log-bin activated 
show variables like '%log%bin';
select @@log_bin;
exit
```

## Exercise 

```
cd /var/lib/mysql
# Das letzte nehmen, wenn mehrere da sind 
mysqlbin -vv mysqld-bin.000001
mysql
```

```
create schema kurs;
exit;
```

```
# Das letzte nehmen, wenn mehrere da sind 
mysqlbinlog -vv mysqld-bin.000001
```
