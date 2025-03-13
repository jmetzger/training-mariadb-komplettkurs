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
mariabackup -uroot -p<password-hier-rein> --target-dir=C:\Users\Administrator\Desktop\backups\2025031301 --prepare 
```

### Schritt 4: Recover 

```
# 1. mariadb-dienst stoppen
# 2. Umbenennen des Ordner data -> data.bkup  
mariabackup -uroot -p<password-hier-rein> --target-dir=C:\Users\Administrator\Desktop\backups\2025031301 --prepare
# 3. Rechte anpassen 
# 4. Dienst starten 
```

## Ref. 
https://mariadb.com/kb/en/full-backup-and-restore-with-mariabackup/
