# Galera Installation and Configuration  (Ubuntu)

## Installation nodes (1, 2, 3) 

```
sudo apt-get install apt-transport-https curl
sudo mkdir -p /etc/apt/keyrings
sudo curl -o /etc/apt/keyrings/mariadb-keyring.pgp 'https://mariadb.org/mariadb_release_signing_key.pgp'
```

```
nano /etc/apt/sources.list.d/mariadb.sources
```


```
# MariaDB 10.6 repository list - created 2023-09-20 11:30 UTC
# https://mariadb.org/download/
X-Repolib-Name: MariaDB
Types: deb
# deb.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.
# URIs: https://deb.mariadb.org/10.6/ubuntu
URIs: https://ftp.agdsn.de/pub/mirrors/mariadb/repo/10.6/ubuntu
Suites: jammy
Components: main main/debug
Signed-By: /etc/apt/keyrings/mariadb-keyring.pgp
Architectures: amd64
```

```
apt update
apt install -y mariadb-server mariadb-backup rsync
```

## Node 1 

### Configure 

```
# Schritt 1: Create config 
nano /etc/mysql/mariadb.conf.d/z_galera.cnf
```

```
[mysqld]
binlog_format=ROW
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0
# Set to 1 sec instead of per transaction
# for better performance // Attention: You might loose data on power
innodb_flush_log_at_trx_commit=0
# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so
# Galera Cluster Configuration
wsrep_cluster_name="test_cluster_jm1"
wsrep_cluster_address="gcomm://192.168.56.103,192.168.56.104,192.168.56.105"
wsrep_node_address=192.168.56.103
# Galera Synchronization Configuration
wsrep_sst_method=rsync
```

### Stop the server and bootstrap cluster (only on first node) 

```
# setup first node in cluster 
systemctl stop mariadb 
galera_new_cluster # statt systemctl start mariadb 
```

### Check if cluster is running 

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


## Node 2

### Configure 

```
# Schritt 1: Create config 
nano /etc/mysql/mariadb.conf.d/z_galera.cnf
```

```
[mysqld]
binlog_format=ROW
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0
# Set to 1 sec instead of per transaction
# for better performance // Attention: You might loose data on power
innodb_flush_log_at_trx_commit=0
# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so
# Galera Cluster Configuration
wsrep_cluster_name="test_cluster_jm1"
wsrep_cluster_address="gcomm://192.168.56.103,192.168.56.104,192.168.56.105"
# ----> KORRIGIEREN 
wsrep_node_address=192.168.56.104
# Galera Synchronization Configuration
wsrep_sst_method=rsync
```

### Stop the server and bootstrap cluster (only on first node) 

```
# setup first node in cluster 
systemctl stop mariadb 
systemctl start mariadb 
```

### Check if cluster is running 

```
mysql> show status like 'wsrep%'\G
*************************** 38. row ***************************
Variable_name: wsrep_local_state_comment
        Value: Synced
*************************** 56. row ***************************
Variable_name: wsrep_cluster_size
        Value: 2
*************************** 58. row ***************************
Variable_name: wsrep_cluster_status
        Value: Primary
*************************** 59. row ***************************
Variable_name: wsrep_connected
        Value: ON

```

## Node 3

### Configure 

```
# Schritt 1: Create config 
nano /etc/mysql/mariadb.conf.d/z_galera.cnf
```

```
[mysqld]
binlog_format=ROW
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0
# Set to 1 sec instead of per transaction
# for better performance // Attention: You might loose data on power
innodb_flush_log_at_trx_commit=0
# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so
# Galera Cluster Configuration
wsrep_cluster_name="test_cluster_jm1"
wsrep_cluster_address="gcomm://192.168.56.103,192.168.56.104,192.168.56.105"
# ----> KORRIGIEREN 
wsrep_node_address=192.168.56.105
# Galera Synchronization Configuration
wsrep_sst_method=rsync
```

### Stop the server and bootstrap cluster (only on first node) 

```
# setup first node in cluster 
systemctl stop mariadb 
systemctl start mariadb 
```

### Check if cluster is running 

```
mysql> show status like 'wsrep%'\G
*************************** 38. row ***************************
Variable_name: wsrep_local_state_comment
        Value: Synced
*************************** 56. row ***************************
Variable_name: wsrep_cluster_size
        Value: 3
*************************** 58. row ***************************
Variable_name: wsrep_cluster_status
        Value: Primary
*************************** 59. row ***************************
Variable_name: wsrep_connected
        Value: ON

```
