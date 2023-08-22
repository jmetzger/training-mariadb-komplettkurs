# Start,stop and enabling of mariadb

## start/stop/status 

```
# als root - user 
systemctl status mariadb
systemctl stop mariadb 
systemctl start mariadb 

# 
systemctl restart mariadb
```

## enable / disable 

  * autostart aktivieren (beim Booten des Systems automatisch starten) 

```
# enable to be started after reboot 
systemctl enable mariadb

# autostart deaktivieren
systemctl disable mariadb

# autostart config abfragen
systemctl is-enabled mariadb  
```

## how is service configured / systemd-wise 

```
systemctl cat mariadb 

```
