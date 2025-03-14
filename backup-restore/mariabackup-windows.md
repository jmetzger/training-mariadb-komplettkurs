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
mariabackup -uroot -p<password-hier-rein> --target-dir=C:\Users\Administrator\Desktop\backups\2025031301 --copy-back
# 3. my.ini aus data.bkup nach data kopieren
# RECHTE: 4. Benutzer NT Service\MariaDB mit Vollzugriff (!!) unter Sicherheit hinterlegen für data - Verzeichnis
(gleicher Benutzer wie beim Dienst -> MariaDB --> kann auch von dort kopiert werden)
```

![image](https://github.com/user-attachments/assets/6954c268-6b18-4bb1-a270-45c42abec69b)

![image](https://github.com/user-attachments/assets/7eb4499c-a21d-4f29-b083-5b94bad4d2e8)

![image](https://github.com/user-attachments/assets/3f47f9b6-bff0-4e8b-9349-865de916e04f)

```
# 4. Dienst starten 
```

## mariabackup ohne Passwort - Eingabe verwenden 

### Variante 1: Umgebungvariable verwenden


1. **Umgebungsvariable setzen:**
   Du kannst die Umgebungsvariable `MYSQL_PWD` verwenden, um das Passwort für die MariaDB-Verbindung anzugeben.

   - Öffne die Eingabeaufforderung (CMD) und setze die Umgebungsvariable temporär:
     ```cmd
     set MYSQL_PWD=deinpasswort
     ```

1. **`mariadb-backup` ausführen:**
   Nachdem du die Umgebungsvariable gesetzt hast, kannst du `mariadb-backup` ausführen, ohne das Passwort anzugeben:
   ```cmd
   mariadb-backup --user=deinbenutzername --host=deinhost --backup-dir=C:\Pfad\zum\Sicherungsordner
   ```


## Ref. 
https://mariadb.com/kb/en/full-backup-and-restore-with-mariabackup/
