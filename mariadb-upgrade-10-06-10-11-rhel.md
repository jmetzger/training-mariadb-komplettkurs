# Upgrade MariaDB 10.6 -> 10.11 (RHEL,Rocky,Centos)

```
# Step 0;
# Sicherung anlegen (mysqldump / mariabackup) 

# Step 1:
# Change version in 
# or where you have your repo definition
# Change 10.6 -> 10.11 
/etc/apt/sources.list
apt update
# Step 2:
systemctl stop mariadb 

# STep 3:
apt list --installed | grep -i mariadb

# Step 3
apt remove -y mariadb*10.6
apt autoremove -y 

# Step 4
sudo apt install -y mariadb-server # Achtung muss 10.11 sein 
apt list --installed | grep -i mariadb # ist wirklich 10.11 installiert. 

# Step 4.5 
# Check if old config files were saved as .rpmsave after delete of package 10.4 
cd /etc/mysql/mariadb.conf.d/
ls -la 50-server.cnf*
# e.g. 


# Step 5:
systemctl start mariadb 
systemctl enable mariadb

# Only necessary, if mysql_upgrade_info is not 10.11.x in /var/lib/mysql
mysql_upgrade # After that mysql_upgrade_info will be present in /var/lib/mysql with version-info
```
