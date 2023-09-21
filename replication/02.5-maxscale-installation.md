# MaxScale - LoadBalancer // Installation and Configuration (Debian/Ubuntu)

##  Why do Loadbalancing with MaxScale ?


*  Cluster node transparent to application 
    * Application does not see single nodes 

*  If one node fails you will have no downtime 
    * In opposite: To talking to this node directly 

## License Implications since 2.x

*  MariaDB MaxScale >= 2.0 is licensed under MariaDB BSL.

*  maximum of three servers in a commercial context. 
    * Any more, and you’ll need to buy their commercial license.

*  MariaDB MaxScale 2.1.0 will be released under BSL 1.1 from the start

*  Each release transitions in about max 4 years to GPL 


## Current version 

  * Current Version is maxscale 6 (05/2023)

## The MaxScale load-balancer and its components

*  Routers 
*  Listeners 
*  Filters
*  Servers (backend database server)

###  Filters

*  Logging Filters
*  Statement rewriting filters
*  Result set manipulation filters 
*  Pipeline control filters
    * e.g. tee and send to a second server

*  Ref: https://mariadb.com/kb/en/mariadb-maxscale-6-regex-filter/

## Documentation - maxctrl 

  * https://mariadb.com/kb/en/mariadb-maxscale-6-maxctrl/


## Installation and Setup

### Installation
	
```
apt update
apt install apt-transport-https

# Setting up the repos 
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
# Installing maxscale
apt install maxscale
```

### Setup (Part 1: MaxScale db-user)

  * Do this on one of the galera nodes 
  * Adjust IP !! // best-> private ip from maxscale  -> e.g. 10.135.0.30

```bash
# IP FROM MAXSCALE
# Setup privileges on cluster nodes
# It is sufficient to set it on one node, because 
# it will be synced to all the other nodes
# on node 1 
CREATE USER 'maxscale'@'10.135.0.x' IDENTIFIED BY 'P@ssw0rd';
#
GRANT SELECT ON mysql.db TO 'maxscale'@'10.135.0.x';
GRANT SELECT ON mysql.user TO 'maxscale'@'10.135.0.x';
GRANT SELECT ON mysql.tables_priv TO 'maxscale'@'10.135.0.x';
#
GRANT SELECT ON mysql.columns_priv TO 'maxscale'@'10.135.0.x';
GRANT SELECT ON mysql.proxies_priv TO 'maxscale'@'10.135.0.x';
#
GRANT SHOW DATABASES ON *.* TO 'maxscale'@'10.135.0.x';
# Needed for maxscale 
GRANT SELECT ON mysql.procs_priv TO 'maxscale'@'10.135.0.x';
GRANT SELECT ON mysql.roles_mapping TO 'maxscale'@'10.135.0.x';
```

```
### Testing if user works 
# On maxscale - server 
apt update 
apt install mariadb-client 
# Test the connection 
# Verbindung sollte aufgebaut werden 
mysql -u maxscale -p -h <ip-eines-der-nodes>
mysql>show databases 
```

##### Setup (Part 2: Configuration)

```
# /etc/maxscale.cnf

[maxscale]

threads=auto
syslog=0
maxlog=1
log_warning=1
log_notice=1
log_info=0
log_debug=0

[Galera-Monitor]

type=monitor
module=galeramon
servers=server1,server2,server3
user=maxscale
password=P@ssw0rd
monitor_interval=2000ms
disable_master_failback=1
# Needs to be false, when block sst-method like rsync is used
available_when_donor=false 

[RW-Split-Router]
type=service
router=readwritesplit
servers=server1,server2,server3
user=maxscale
password=P@ssw0rd


[RW-Split-Listener]
type=listener
service=RW-Split-Router
protocol=MariaDBClient
port=3306

[server1]
type=server
address=142.93.98.60
port=3306
protocol=MariaDBBackend

[server2]
type=server
address=142.93.103.153
port=3306
protocol=MariaDBBackend

[server3]
type=server
address=142.93.103.246
port=3306
protocol=MariaDBBackend
```

```
# Start

systemctl start maxscale  
```


```
# What does the log say ? 

# /var/log/maxscale/maxscale.log 
```

## maxctrl 

```
maxctrl list servers
maxctrl set server server1 maintenance
maxctrl clear server server1 maintenance
maxctrl show server server1
maxctrl list services 
maxctrl show service ReadWrite-Split-Router 
```

## Exercise 

```
# Create user for client and maxscale on one of the galera nodes 
# IP-Adresse from MaxScale 
create database if not exists training; 
create user joe@'10.135.0.45' identified by 'password';
grant all on training.* to joe@'10.135.0.45'; 
```

```
# Create same user for client 
# adjust x in ip by ip - part of client 
create database if not exists training; 
create user joe@'10.135.0.x' identified by 'password';
grant all on training.* to joe@'10.135.0.x'; 

```

```
# Fire Up client-server (used only for mysql-client 
# there
# simply take the one from the repo 
apt install mariadb-client 
# then connect 
mysql -ujoe -p -h<ip-of-maxscale>
mysql>create database training; use training; create table trainee (id int,name varchar(50), primary key(id)); insert into trainee (id, name) values (1,'Jochen'); select @@hostname;
mysql>use training; select * from trainee; select @@hostname;
# These should be different servers 
```
