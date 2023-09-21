# Upgrade MariaDB 10.6 -> 10.11 (Debian/Ubuntu) 

## Step 1: Backup anlegen. 

  * Eventually not necessary for slave, because we can set it up anyways (with mariabackup from master)
  * Best Practice, start wih slave 


## Step 2: Change Version .sources or .list - file 

```
# Change version in 
# or where you have your repo definition
# Change 10.6 -> 10.11 
/etc/apt/sources.list
# or 
/etc/apt/sources.list.d/mariadb.soruces 
```

```
apt update
```

```
systemctl stop mariadb 
```

```
apt list --installed | grep -i mariadb
```

```
apt remove -y mariadb*10.6
apt autoremove -y 
```

```
sudo apt install -y mariadb-server # Achtung muss 10.11 sein 
apt list --installed | grep -i mariadb # ist wirklich 10.11 installiert. 
```

## Step 3: Check config and start 

```
cd /etc/mysql/mariadb.conf.d/
ls -la 50-server.cnf*
# e.g. 
```

```
systemctl start mariadb 
systemctl enable mariadb
```

## Step 4: Check if mysql_upgrade already was done ?  

```
# Only necessary, if mysql_upgrade_info is not 10.11.x in /var/lib/mysql  
mysql_upgrade # After that mysql_upgrade_info will be present in /var/lib/mysql with version-info 
```

## Reference 

  * https://mariadb.com/kb/en/upgrading-from-mariadb-10-6-to-mariadb-10-11/
