# Installation Ubuntu 22.04 LTS / Debian  

## Setup repo and install

 * https://downloads.mariadb.org/mariadb/repositories/

```
sudo apt-get install apt-transport-https curl
sudo mkdir -p /etc/apt/keyrings
sudo curl -o /etc/apt/keyrings/mariadb-keyring.pgp 'https://mariadb.org/mariadb_release_signing_key.pgp'
```

```
nano /etc/apt/sources.list.d/mariadb.sources 
```

```
# MariaDB 10.6 repository list - created 2023-09-18 08:26 UTC
# https://mariadb.org/download/
X-Repolib-Name: MariaDB
Types: deb
# deb.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.
# URIs: https://deb.mariadb.org/10.6/ubuntu
URIs: https://ftp.agdsn.de/pub/mirrors/mariadb/repo/10.6/ubuntu
Suites: jammy
Components: main main/debug
Signed-By: /etc/apt/keyrings/mariadb-keyring.pgp
# added by trainer because of warning with i386
Architectures: amd64
```

```
sudo apt-get update
sudo apt-get install mariadb-server
```


## Secure installation 

```
mariadb-secure-installation 
# OR: if not present before 10.4 
mysql_secure_installation 
```
