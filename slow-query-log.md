# Slow Query Log 

## Walkthrough (MariaDB from 10.11) 

```
# Step 1
# /etc/my.cnf.d/mariadb-server.cnf 
# or: debian /etc/mysql/mariadb.conf.d/50-server.cnf 
[mysqld]
slow-query-log 
```

```
systemctl restart mariadb
```

```
mariadb 
```

```
-- kleinst mögliche Zeit 0.000001 Sekunden
SET GLOBAL log_slow_query_time=0.000001
-- in der session setzen, damit das sofort funktioniert
SET log_slow_query_time=0.000001
-- Achtung, steht nach nächstem Neustart wieder auf 10 Sekunden (Default)
```



## Walkthrough (before 10.11) 

```
# Step 1
# /etc/my.cnf.d/mariadb-server.cnf 
# or: debian /etc/mysql/mariadb.conf.d/50-server.cnf 
[mysqld]
slow-query-log 
```

```
systemctl restart mariadb
```

```
# ODER
# Alternative: Step 1a
mysql>SET GLOBAL slow_query_log = 1 
mysql>SET slow_query_log = 1 
```

```
# Step 2: 
# Zeit festlegen, ab wann eine query langsam ist
# global für den Server (greift erst bei der nächsten Session) 
mysql>SET GLOBAL long_query_time = 0.000001;
# und für die aktuelle Session
mysql>SET long_query_time = 0.000001
```

```
# Step 3
# run some time / data
# and look into your slow-query-log 
/var/lib/mysql/hostname-slow.log 
```

## Exercise (mariadb 10.6 from mariadb.org) 

```
# Step 1
# /etc/my.cnf.d/server.cnf 
[mysqld]
slow-query-log 
```

```
# Step 2: restart server
systemctl restart mariadb
mysql
```

```
-- Step 3: set long_query_time (global and in session)
select @@slow_query_log;

-- set and show global 
set global long_query_time = 0.000001;
select @@global.long_query_time;
show global variables like '%long%';

-- (Optional) set and show session (for this session)
set long_query_time = 0.000001;
select @@long_query_time;
show variables like '%long%';

```

```
# Step 4: Import data
cd /usr/src/sakila-db
mysql < sakila-schema.sql
mysql < sakila-data.sql
```

```
# Step 5: what did we log
cd /var/lib/mysql
ls -la server1-slow.log 
less server1-slow.log 
```

## Show queries that do not use indexes 

```
SET GLOBAL log_queries_not_using_indexes=ON;
```

## Geschwätzigkeit (Verbosity) erhöhen 

```
SET GLOBAL log_slow_verbosity='query_plan,explain'
```

## Queries die keine Indizes verwenden 

```
SET GLOBAL log_queries_not_using_indexes=ON;
```


## Reference 

  * https://mariadb.com/kb/en/slow-query-log-overview/

