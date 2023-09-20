# Setting up new slave with master-slave replication 

## Step 1: set server-id 1 and log-bin 

```
# Variante Centos/Rocky 
cd /etc/my.cnf.d
nano z_settings.cnf
```

```
Variante Debian/Ubuntu
cd /etc/mysql/mariadb.conf.d
nano z_settings.cnf
```


```
[mysqld]
server-id = 1
log-bin
```

```
systemctl restart mariadb 
## you should add data, otherwice no gtid will get created if you enable the binlog only from now on
mysql -e "create schema foo;"
```

## Step 2a: Installation on ubuntu/debian (master)

```
apt update
apt install mariadb-backup 
# check if available
mariabackup --version 
```


## Step 2b: Installation on centos/rocky/rhel (master)

```
dnf install -y mariadb-backup 
# check if available
mariabackup --version 
```

## Step 3: Setup mariabackup 

```
# prepare for mariabackup if you use it with root and with unix_socket 
/root/.my.cnf 
[mariabackup]
user=root
```

## Step 4: mariabackup on master 

```
mkdir -p /backups 
# target-dir needs to be empty or not present 
mariabackup --target-dir=/backups/2023092001 --backup 
# apply ib_logfile0 to tablespaces 
# after that ib_logfile0 ->  0 bytes 
mariabackup --target-dir=/backups/2023092001 --prepare 
```

## Step 4.5: Setup slave-server 

```
# start server 
apt update
apt install mariadb-server 

# use the same config-settings as on master
# Why ? Get the same performance
nano z_settings.cnf
```

```
# Example

[mysqld]

server-id=2 # slave please do not use server-id=1 -> must be unique
log-bin

bind-address=0.0.0.0

innodb-buffer-pool-size = 2500MB
innodb-log-file-size=320M
```

```
systemctl restart mariadb
```


## Step 5: Transfer to new slave (from master) 

```
# root@master:
rsync -e ssh -avP /backups/2023092001 kurs@192.168.56.104:/home/kurs
```

## Step 6: Setup replication user on master 

```
# as root@master 
#mysql>
CREATE USER repl@'192.168.56.%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.*  TO 'repl'@'192.168.56.%';
```

## Step 7 (Optional): Test repl user (connect) from slave 

```
# as root@slave 
# you be able to connect to 
mysql -urepl -p -h192.168.56.102
# test if grants are o.k. 
show grants;
```

## Step 8: Set server-id on slave -> 1 + same config as server 1 + log-slave-update

```
cd /etc/my.cnf.d
nano z_settings.cnf
```

```
[mysqld]
server-id              = 2
# activate master bin log, if this slave might be a master later 
log-bin
log-slave-updates
```

```
systemctl restart mariadb 
```

## Step 9: Restore Data on slave 

```
systemctl stop mariadb 
mv /var/lib/mysql /var/lib/mysql.bkup
mariabackup --target-dir=/home/kurs/2023092001 --copy-back 
chown -R mysql:mysql /var/lib/mysql
chmod -R 755 /var/lib/mysql
# only redhat 
# restorecon -vr /var/lib/mysql
systemctl start mariadb
```

## Step 10: master.txt for change command 

```
# root@slave
# $ cat xtrabackup_binlog_info
cd /home/11trainingdo/20210121
cat xtrabackup_binlog_info 
# mariadb-bin.000096 568 0-1-2
```

```
nano /root/master.txt
```

```
SET GLOBAL gtid_slave_pos = "0-1-2";
# /root/master.txt 
# get information from master-databases.sql dump 
CHANGE MASTER TO 
   MASTER_HOST="10.135.0.x", 
   MASTER_PORT=3306, 
   MASTER_USER="repl",  
   MASTER_PASSWORD="password", 
   MASTER_USE_GTID=slave_pos;
```

```
mysql < /root/master.txt 
mysql 
```

```
-- in mysql 
start slave
show slave status 
-- # Looking for
-- Slave_IO_Running: Yes
-- Slave_SQL_Running: Yes

```



## Walkthrough 

  * https://mariadb.com/kb/en/setting-up-a-replication-slave-with-mariabackup/
