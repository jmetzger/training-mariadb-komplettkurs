# Galera Installation and Configuration 

## Preparation: Install mariadb 

```
# On all nodes install mariadb and mariadb-server
dnf install -y mariadb mariadb-server mariadb-server-galera 
```

## Setting up 1st - node

```
nano /etc/my.cnf.d/z_galera.cnf 
```

```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2 
bind-address=0.0.0.0

# for better performance 
innodb_flush_log_at_trx_commit=2

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib64/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster-<your shortcut e.g. r1>"
wsrep_cluster_address="gcomm://10.135.0.3,10.135.0.4,10.135.0.5"
wsrep_node_address=10.135.0.3 

# Galera Synchronization Configuration
wsrep_sst_method=mariabackup
wsrep_sst_auth = mariabackup:mypassword
```



```
systemctl stop mariadb
galera_new_cluster
mysql
```

```
CREATE USER 'mariabackup'@'localhost' IDENTIFIED BY 'mypassword';
GRANT RELOAD, PROCESS, LOCK TABLES, REPLICATION CLIENT ON *.* TO 'mariabackup'@'localhost';
quit
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

```
# Schritt 1: Create config 

/etc/my.cnf.d/z_galera.cnf 
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0
# Set to 1 sec instead of per transaction
# for better performance // Attention: You might loose data on power
innodb_flush_log_at_trx_commit=0
# Galera Provider Configuration
wsrep_on=ON
# centos7 (x86_64)
wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so
# Galera Cluster Configuration
wsrep_cluster_name="test_cluster"
wsrep_cluster_address="gcomm://192.168.56.103,192.168.56.104,192.168.56.105"
wsrep_node_address=192.168.56.103
# Galera Synchronization Configuration
wsrep_sst_method=rsync
```

## Stop the server and bootstrap cluster 

```
# setup first node in cluster 
systemctl stop mariadb 
galera_new_cluster # statt systemctl start mariadb 
```

## Check if cluster is running 

```
mysql> show status like 'wsrep%'\G
*************************** 38. row ***************************
Variable_name: wsrep_local_state_comment
        Value: Synced
*************************** 56. row ***************************
Variable_name: wsrep_cluster_size
        Value: 1
*************************** 57. row ***************************
Variable_name: wsrep_cluster_state_uuid
        Value: 562e5455-a40f-11eb-b8c9-1f32a94e106e
*************************** 58. row ***************************
Variable_name: wsrep_cluster_status
        Value: Primary
*************************** 59. row ***************************
Variable_name: wsrep_connected
        Value: ON

```

### Setup firwealld for galera 

```
firewall-cmd --add-port=3306/tcp --permanent
firewall-cmd --add-port=4567/tcp --permanent
firewall-cmd --add-port=4568/tcp --permanent
firewall-cmd --add-port=4444/tcp --permanent
firewall-cmd --reload 

firewall-cmd --add-port=3306/tcp --permanent; firewall-cmd --add-port=4567/tcp --permanent; firewall-cmd --add-port=4568/tcp --permanent; firewall-cmd --add-port=4444/tcp --permanent; firewall-cmd --reload 


```
