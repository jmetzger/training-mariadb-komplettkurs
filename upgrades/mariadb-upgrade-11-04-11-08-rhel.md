# Upgrade MariaDB 11.4 -> 11.8 (RHEL,Rocky,Centos)

## Walkthrough

```
# Step 0;
# Sicherung anlegen (mysqldump / mariabackup) 

# Step 1:
# Change version in 
# or where you have your repo definition
# Change 11.4 -> 11.8 
cd /etc/yum.repos.d/
nano MariaDB.repo
```

```
# Change version in file from 11.4 -> 11.8
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
dnf list --installed | grep -i mariadb # ist wirklich 11.8 installiert. 

# Step 4.5 
# Check if old config files were saved as .rpmsave after delete of package 11.4
cd /etc/my.cnf.d/
ls -la server.cnf
# Eventually consolidate everything in one file loaded as last entry, e.g.
# z_settings.cnf 

# Step 5:
systemctl start mariadb 
systemctl enable mariadb

# Only necessary, if mysql_upgrade_info is not 11.8 in /var/lib/mysql
mariadb-upgrade # After that mysql_upgrade_info will be present in /var/lib/mysql with version-info
```

## Reference:

  * [Upgrade mariadb 11.4 -> 11.8](https://mariadb.com/docs/server/server-management/install-and-upgrade-mariadb/upgrading/mariadb-community-server-upgrade-paths/upgrading-from-mariadb-11-4-to-mariadb-11-8)
