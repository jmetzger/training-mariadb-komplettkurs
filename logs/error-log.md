# Error Log aktivieren in einer Datei 

## Walkthrough (using log in filesystem)

```
# Ubuntu / Debian 
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

```
[mysqld]
log-error=/var/log/mysql/mariadb-error.log
```

```
systemctl restart mariadb
```
