# Setup galera cluster with mariadb (Ubuntu 22.04 -> MariaDB 10.6 from Repo or Foundation)

## Setup Node 1: 

```
apt update 
apt install -y mariadb-server mariadb-backup 
```

```
nano /etc/mysql/mariadb.conf.d/z_settings.cnf
```

```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2 
bind-address=0.0.0.0

# for better performance // Attention: You might loose data on power outage
innodb_flush_log_at_trx_commit=2

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster-<your shortcut e.g. r1>"
wsrep_cluster_address="gcomm://10.135.0.3,10.135.0.4,10.135.0.5"
wsrep_node_address=10.135.0.3 

# Galera Synchronization Configuration
wsrep_sst_method=mariabackup
wsrep_sst_auth = mariabackup:mypassword
```

```
mysql
```

```
CREATE USER 'mariabackup'@'localhost' IDENTIFIED BY 'mypassword';
GRANT RELOAD, PROCESS, LOCK TABLES, REPLICATION CLIENT ON *.* TO 'mariabackup'@'localhost';
quit
```

```
systemctl stop mariadb
galera_new_cluster
mysql
```

```
show status like 'wsrep%';
quit
```

## Setup Node 2:

```
apt update 
apt install -y mariadb-server mariadb-backup 
```

```
nano /etc/mysql/mariadb.conf.d/z_settings.cnf
```

```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2 
bind-address=0.0.0.0

# for better performance // Attention: You might loose data on power outage
innodb_flush_log_at_trx_commit=2

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster-<your shortcut e.g. r1>"
wsrep_cluster_address="gcomm://10.135.0.3,10.135.0.4,10.135.0.5"
wsrep_node_address=10.135.0.4

# Galera Synchronization Configuration
wsrep_sst_method=mariabackup
wsrep_sst_auth = mariabackup:mypassword
```

```
systemctl restart mariadb
```

## Setup Node 3:

```
apt update 
apt install -y mariadb-server mariadb-backup 
```

```
nano /etc/mysql/mariadb.conf.d/z_settings.cnf
```

```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2 
bind-address=0.0.0.0

# for better performance // Attention: You might loose data on power outage
innodb_flush_log_at_trx_commit=2

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster-<your shortcut e.g. r1>"
wsrep_cluster_address="gcomm://10.135.0.3,10.135.0.4,10.135.0.5"
wsrep_node_address=10.135.0.5

# Galera Synchronization Configuration
wsrep_sst_method=mariabackup
wsrep_sst_auth = mariabackup:mypassword
```

```
systemctl restart mariadb
```


