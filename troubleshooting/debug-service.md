# Debug Service 

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
