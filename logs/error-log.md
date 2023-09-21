# Error-log 

## Was ist das ?

  * Zeigt alle Ausgaben beim Starten und Stoppen des MariaDB - Dienstes
  * Nicht nur Fehler, sondern alles

## Error Log aktivieren in Datei -> Walkthrough (using log in filesystem)

```
# Ubuntu / Debian 
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

```
[mysqld]
log-error=/var/log/mysql/mariadb-error.log
```

```
systemctl restart mariadb
```

## Problem: Keine Ausgabe über journal 

  * journalctl -u mariadb -> keine Ausgabe bzw. keine Einträge

### Lösung 

```
# Prüfen ob systemd-journal - dienst läuft bzw. einfach noch mal neu starten
systemctl restart systemd-journald.services
```
    
