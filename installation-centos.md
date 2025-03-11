# Installation Centos / Rocky Linux 

## Install from Distribution 


```
dnf search mariadb
# find version 
dnf info mariadb-server 
dnf install -y mariadb-server mariadb
```


## Install from MariaDB Foundation (Repo) 

### Find Repo Settings 

  * [https://mariadb.org/download/?t=repo-config&d=Red+Hat+Enterprise+Linux+9&v=10.6&r_m=agdsn](https://mariadb.org/download/?t=repo-config&d=Red+Hat+Enterprise+Linux+9&v=11.4&r_m=agdsn)

### Setup Repo MariaDB - Server 11.4

```
# Setup repo 
# nano /etc/yum.repos.d/MariaDB.repo
```

```
# MariaDB 10.6 RedHatEnterpriseLinux repository list - created 2023-09-21 11:52 UTC
# https://mariadb.org/download/
[mariadb]
name = MariaDB
# rpm.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.
# baseurl = https://rpm.mariadb.org/10.6/rhel/$releasever/$basearch
baseurl = https://ftp.agdsn.de/pub/mirrors/mariadb/yum/10.6/rhel/$releasever/$basearch
# gpgkey = https://rpm.mariadb.org/RPM-GPG-KEY-MariaDB
gpgkey = https://ftp.agdsn.de/pub/mirrors/mariadb/yum/RPM-GPG-KEY-MariaDB
gpgcheck = 1
```

```
# Install
sudo dnf install -y install MariaDB-server MariaDB-client
sudo systemctl start mariadb # always works - systemd - alias 
sudo systemctl status mariadb # Findout real service - name
# like Windows-Autostart
sudo systemctl enable mariadb  
sudo systemctl status mariadb 
```


## Secure installation 

  * Removes anonymous users (users without username)
  * Remove test-db
  * Eventually removes root-user connection from outside 

```
mariadb-secure-installation 
# OR: if not present before 10.4 
mysql_secure_installation 
```
