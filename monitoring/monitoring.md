# Monitoring

## What to monitor 

### System 

  * Last auf dem System (top) 
  * Festplatte (z.B. 85% voll ?) df /var/lib/mysql
  * Swap (Wenn geswappt wird ist Hopfen und Malz verloren) 
  
### Erreichbarkeit 

  * Server per ping erreichen (mysqladmin ping -h ziel-ip) 
  * Einlogbar ? (mysqladmin ping -h ziel-ip -u control_user)
 
### Platte aka IO-Subsystem (iostats)

  * http://schulung.t3isp.de/documents/pdfs/mysql/mysql-performance.pdf

| --       | --          | -- |
| ------------- |:-------------:| -----:|
| Read/Write requests	      | IOPS (Input/Output operations per second) | -- |
| Average IO wait	| Time that queue operations have to wait for disk access |   -- |
| Average Read/Write time | Time it takes to finish disk access operations (latency) |  -- |
| Read/Write bandwidth | Data transfer from and towards your disk | -- |

### General mysql metrics 

 ```
 mysql -E -e "select variable_value from information_schema.session_status where variable_name = 'uptime'";
 
 # max connections 
 MariaDB [(none)]> show status like 'max_used_connections';
+----------------------+-------+
| Variable_name        | Value |
+----------------------+-------+
| Max_used_connections | 1     |
+----------------------+-------+
1 row in set (0.001 sec)

MariaDB [(none)]> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
1 row in set (0.001 sec)
 
mysqladmin status 
# you will find uptime here in seconds 
 
```

| Metric	| Comments	| Suggested Alert |
| ------------- |:-------------:| -----:|
| Uptime	| Seconds since the server was started. We can use this to detect respawns.	 | When uptime is < 180. (seconds)  |
| Threads_connected	| Number of clients currently connected. If none or too high, something is wrong.	| None |
| Max_used_connections |	Max number of connections at a time since server started. (max_used_connections / max_connections) indicates if you could run out soon of connection slots.|	When connections usage is > 85%. |
| Aborted_connects |	Number of failed connection attempts. When growing over a period of time either some credentials are wrong or we are being attacked. show status like 'Aborted_connects'	| When aborted connects/min > 3. |

### InnoDB 

| Metric | Coments | Suggested Alert | 
| ------------- |:-------------:| -----:|
| Innodb_row_lock_waits	| Number of times InnoDB had to wait before locking a row.	| None |
| Innodb_buffer_pool_wait_free	| Number of times InnoDB had to wait for memory pages to be flushed. If too high, innodb_buffer_pool_size is too small for current write load.	| None | 

### Query tracking 

| Metric	| Comments	| Suggested Alert | 
| ------------- |:-------------:| -----:|
| Slow_queries	| Number of queries that took more than long_query_time seconds to execute. Slow queries generate excessive disk reads, memory and CPU usage. Check slow_query_log to find them.	| None | 
| Select_full_join	| Number of full joins needed to answer queries. If too high, improve your indexing or database schema.	| None |
| Created_tmp_disk_tables	| Number of temporary tables (typically for joins) stored on slow spinning disks, instead of faster RAM.	| None |
| (Full table scans) Handler_read%	Number of times the system reads the first row of a table index. (if 0 a table scan is done - because no key was read). Sequential reads might indicate a faulty index.	None

### Track Errors 

```
journalctl -u mariadb | grep -i Error
```

### Monitoring with pmm (Percona Management Monitoring) 

https://pmmdemo.percona.com

[Documentation](https://www.percona.com/doc/percona-monitoring-and-management/2.x/details/commands/pmm-admin.html)
