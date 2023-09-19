# InnoDB 

## How big is the innodb buffer currently (setup) ?

```
mysql>select @@innodb_buffer_pool_size; 
mysql>show variables like '%buffer%';
```

## Innodb buffer pool

  * How much data fits into memory 
  * Free buffers = pages of 16 Kbytes 
  * Free buffer * 16Kbytes = free innodb buffer pool in KByte  
```
# does not in windows -> pager grep
pager grep -i 'free buffers'
# does not work with workbench or heidisql because of formatting + \G only works in client
show engine innodb status \G
Free buffers       7905
1 row in set (0.00 sec)
```

## Innodb buffer pool stats with status 

```
# Also works in heidisql or workbench 
show status like '%buffer%';

```

## Overview innodb server variables / settings 

  * https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html

## Change innodb_buffer_pool 

```
# /etc/mysql/mysql.conf.d/mysqld.cnf 
# 70-80% of memory on dedicated mysql
[mysqld]
innodb-buffer-pool-size=6G

#
systemctl restart mysql

# 
mysql
mysql>show variables like 'innodb%buffer%';
```
## problems, when dynamically increasing buffer 

  * https://www.percona.com/blog/2018/06/19/chunk-change-innodb-buffer-pool-resizing/


## innodb_log_buffer_size  

```
1 commit should fit in this buffer 

Question: In your application are your commits bigger or smaller 


```


## innodb_flush_method 

```
Ideally O_DIRECT on Linux, but please test it, if it really works well. 
```

## 	innodb_flush_log_at_trx_commit

```
When is flushing done from innodb_log_buffer to log.
Default: 1 : After every commit 
-> best performance 2. -> once per second

# Good to use 2, if you are willing to loose 1 second of data on powerfail 
```

## innodb_flush_neighbors 

```
# on ssd disks set this to off, because there is no performance improvement 
innodb_flush_neighbors=0 

# Default = 1 

```
## innodb_log_file_size 

```
# Should hold 60-120 min of data flow 
# Calculate like so:
https://www.percona.com/blog/2008/11/21/how-to-calculate-a-good-innodb-log-file-size/

```

## skip-name-resolv.conf 

```
# work only with ip's - better for performance 
/etc/my.cnf 
skip-name-resolve
```

  * https://nixcp.com/skip-name-resolve/


## Ref:

  * https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool-resize.html
  

## Privileges for show engine innodb status 

```
 show engine innodb status \G
ERROR 1227 (42000): Access denied; you need (at least one of) the PROCESS privilege(s) for this operation

```
