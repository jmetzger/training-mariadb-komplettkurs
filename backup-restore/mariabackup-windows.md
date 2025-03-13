# Mariabackup (Windows) 

## Prerequisites 

 * On Windows Mariabackup is already installed by default installer

## Walkthrough (Windows)

### Schritt 2: Backup erstellen 

```
# Auf dem Desktop bspw. backups - Ordner anlegen 
mariabackup -uroot -p<password-hier-rein> --target-dir=C:\Users\Administrator\Desktop\backups\2025031301 --backup
```

### Schritt 3: Prepare durchführen 

```
# apply ib_logfile0 to tablespaces 
# after that ib_logfile0 ->  0 bytes
mariabackup --target-dir=/backups/2023091901 --prepare 
```

### Schritt 4: Recover 

```
systemctl stop mariadb 
mv /var/lib/mysql /var/lib/mysql.bkup 
mariabackup --target-dir=/backups/2023091901 --copy-back 
chown -R mysql:mysql /var/lib/mysql
chmod -R 755 /var/lib/mysql # otherwice socket for unprivileged user does not work
systemctl start mariadb
systemctl status mariadb 
```

## Walkthrough (Redhat/Centos/Rocky Linux)

### Schritt 1: Grundkonfiguration (vor 11.4) 

```
# user eintrag in /root/.my.cnf
[mariabackup]
user=root 
# pass is not needed here, because we have the user root with unix_socket - auth 
# or generic 
# /etc/my.cnf.d/mariabackup.cnf
[mariabackup]
user=root
```

## Schritt 2: Backup erstellen 

```
mkdir /backups 
# target-dir needs to be empty or not present 
mariabackup --target-dir=/backups/2023091901 --backup 
```

## Schritt 3: Prepare durchführen 

```
# apply ib_logfile0 to tablespaces 
# after that ib_logfile0 ->  0 bytes
mariabackup --target-dir=/backups/2023091901 --prepare 
```

## Schritt 4: Recover

```
systemctl stop mariadb 
mv /var/lib/mysql /var/lib/mysql.bkup 
mariabackup --target-dir=/backups/2023091901 --copy-back 
chown -R mysql:mysql /var/lib/mysql
chmod -R 755 /var/lib/mysql # otherwice socket for unprivileged user does not work
# Does not work !!! Because of selinux // does not start
# ls -laZ /var/lib
systemctl start mariadb 

## important for selinux if it does not work
## mariadb 10.6 from mariadb does not have problems here !
## does not start
restorecon -vr /var/lib/mysql 
systemctl start mariadb

## Cleanup if everything works 
rm -fR /var/lib/mysql/mysql.bkup 
```

## Ref. 
https://mariadb.com/kb/en/full-backup-and-restore-with-mariabackup/
