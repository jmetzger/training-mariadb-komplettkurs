# Delete binaray logs 

## What ways do we have to delete binary logs (purge) 

  * Global Variable or in config: expire_logs_days
  * Global Variable or in config:
    * binlog_expire_logs_seconds 
    * https://mariadb.com/kb/en/replication-and-binary-log-system-variables/#binlog_expire_logs_seconds
  * SQL-Kommando: PURGE BINARY LOGS TO
  * Automatically with mysql -> param: --delete-master-logs

## Best practice 

  * Do it when performing backup, either with --delete-master-logs (PIT-Recovery), or in backup script with PURGE BINARY LOGS

