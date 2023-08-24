# Upgrade MariaDB 10.6 -> 10.11 (RHEL,Rocky,Centos)

## Walkthrough

```
# Step 0;
# Sicherung anlegen (mysqldump / mariabackup) 

# Step 1:
# Change version in 
# or where you have your repo definition
# Change 10.6 -> 10.11 
cd /etc/apt/yum.repos.d/
nano MariaDB.repo
```

```
# Change version in file from 10.6 -> 10.11
# Save + quit 
```


```
# Step 2:
systemctl stop mariadb 

# Step 3
dnf remove -y MariaDB-* 
# verify nothing is present 
dnf list installed | grep -i mariadb 

# Step 4
dnf install -y MariaDB-server MariaDB-backup  
dnf list --installed | grep -i mariadb # ist wirklich 10.11 installiert. 

# Step 4.5 
# Check if old config files were saved as .rpmsave after delete of package 10.4 
cd /etc/my.cnf.d/
ls -la server.cnf
# Eventually consolidate everything in one file loaded as last entry, e.g.
# z_settings.cnf 

# Step 5:
systemctl start mariadb 
systemctl enable mariadb

# Only necessary, if mysql_upgrade_info is not 10.11.x in /var/lib/mysql
mysql_upgrade # After that mysql_upgrade_info will be present in /var/lib/mysql with version-info
```

## Reference:

  * https://mariadb.com/kb/en/upgrading-from-mariadb-10-6-to-mariadb-10-11/
