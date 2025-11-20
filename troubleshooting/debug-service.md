# Debug Service 

## Fehler erzeugen (Rocky) 

```
cd /etc
nano my.cnf.d/server.cnf
```

```
# konfiguratiosoption, die es nicht gibt
# in den bereich: mysqld
gummitulpe=23M
```

```
# Speichern
systemctl restart mariadb
```


## Fehler erzeugen (Ubuntu) 

```
cd /etc/mysql/mariadb.d
nano 50-server.cnf
```

```
# konfiguratiosoption, die es nicht gibt
# ans ende
gummitulpe=23M
```

```
# Speichern
systemctl restart mariadb
```



## Walkthrough 

```
# Service is not restarting - error giving
systemctl restart mariadb.service 

# Step 1 : status -> what do the logs tell (last 10 lines) 
systemctl status mariadb.service 

# no findings -> step 2:
journalctl -xeu mariadb.service

# no findings -> step 3:
# search specific log for service 
# and eventually need to increase the log level
# e.g. with mariadb (find through internet research)
less /var/log/mysql/error.log 
# or
less /var/log/mariadb/mariadb.log 

# Nicht fündig -> Schritt 4
# Allgemeines Log
# Debian/Ubuntu 
/var/log/syslog
# Redhat/Centos 
/var/log/messages 
```

## Find errors in logs quickly

```
cd /var/log/mysql 
# -i = case insensitive // egal ob gross- oder kleingeschrieben
cat error.log | grep -i error
```

## Find configuration - option in config  - files 

```
grep -r datadir /etc 

```
