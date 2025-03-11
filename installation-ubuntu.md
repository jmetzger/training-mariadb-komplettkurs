# Installation Ubuntu 24.04 LTS / Debian  

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
# MariaDB 11.4 repository list - created 2025-03-11 10:13 UTC
# https://mariadb.org/download/
X-Repolib-Name: MariaDB
Types: deb
# deb.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.
# URIs: https://deb.mariadb.org/11.4/ubuntu
URIs: https://mirror1.hs-esslingen.de/pub/Mirrors/mariadb/repo/11.4/ubuntu
Suites: noble
Components: main main/debug
Signed-By: /etc/apt/keyrings/mariadb-keyring.pgp
```

```
sudo apt-get update
sudo apt search mariadb 
sudo apt-get install mariadb-server
```

```
systemctl status mariadb
```


## Secure installation 

```
mariadb-secure-installation 
# OR: if not present before 10.4 
mysql_secure_installation 
```
