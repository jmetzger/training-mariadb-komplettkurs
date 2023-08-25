# Setting up new slave with master-slave replication 

## Step 1: set server-id 1 and log-bin 

```
cd /etc/my.cnf.d
nano z_settings.cnf
```

```
[mysqld]
server-id = 1
log-bin
```

```
systemctl restart mariadb 
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
mkdir /backups 
# target-dir needs to be empty or not present 
mariabackup --target-dir=/backups/20210121 --backup 
# apply ib_logfile0 to tablespaces 
# after that ib_logfile0 ->  0 bytes 
mariabackup --target-dir=/backups/20210121 --prepare 
```

## Step 5: Transfer to new slave (from master) 

```
# root@master:
rsync -e ssh -avP /backups/20210121 11trainingdo@10.135.0.x:/home/11trainingdo
```

## Step 6: Setup replication user on master 

```
# as root@master 
#mysql>
CREATE USER repl@'10.135.0.%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.*  TO 'repl'@'10.135.0.%';
```

## Step 7 (Optional): Test repl user (connect) from slave 

```
# as root@slave 
# you be able to connect to 
mysql -urepl -p -h10.135.0.x
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
log-slave-update
```

```
systemctl restart mariadb 
```

## Step 9: Restore Data on slave 

```
systemctl stop mariadb 
mv /var/lib/mysql /var/lib/mysql.bkup
mariabackup --target-dir=/home/11trainingdo/20210121 --copy-back 
chown -R mysql:mysql /var/lib/mysql
chmod -R 755 /var/lib/mysql
restorecon -vr /var/lib/mysql
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
mysql < master.txt 
# or: copy paste into mysql> 
```

```
-- mysql>
start slave
show slave status 
-- # Looking for
-- Slave_IO_Running: Yes
-- Slave_SQL_Running: Yes

```



## Walkthrough 

  * https://mariadb.com/kb/en/setting-up-a-replication-slave-with-mariabackup/
