# Binlog 

## Binlog - Wann ? 

  * PIT (Point-in-Time) - Recovery
  * Master/Slave - Replication
  * Galera Cluster (MariaDB Cluster)

## Binlog aktivieren (Centos)

```
# /etc/my.cnf.d/server.cnf 
[mysqld]
log-bin 

# Server neu starten 
systemctl restart mariadb 
```

## Alte Logs automatisch löschen 

  * https://mariadb.com/kb/en/replication-and-binary-log-system-variables/#expire_logs_days


## Rowbasiertes Logging aktivieren

```
# /etc/my.cnf.d/server.cnf 
[mysqld]
log-bin 
# Server neu starten 
systemctl restart mariadb 
```

## binlog auslesen 

```
cd /var/lib/mysql
# Zeigt auch mit Kommentar die SQL-Statements an die bei ROW-basierten binlog ausgeführt werden
mysqlbinlog -vv mysqld-bin.000001
```

## Wie finde ich raus, welches binlog aktiv ist ? 

```
# mysql -client starten
mysql> show master status;
```
