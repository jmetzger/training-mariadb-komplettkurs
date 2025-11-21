# MariaDB Security Best Practices

## 1. Initial Hardening
```bash
mysql_secure_installation
```
- Root-Passwort setzen
- Anonyme Benutzer entfernen
- Remote-Root-Login deaktivieren
- Test-Datenbank löschen

## 2. Benutzer & Rechte
```sql
-- Prinzip der minimalen Rechte
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'strong_password';
GRANT SELECT, INSERT, UPDATE ON mydb.* TO 'app_user'@'localhost';

-- Keine Wildcards bei Host
-- Statt '%' spezifische IPs/Hosts verwenden
```

## 3. Netzwerk & Verbindungen
```ini
[mysqld]
bind-address = 127.0.0.1  # Nur localhost (bei Bedarf spezifische IP)
skip-networking           # Wenn nur lokale Verbindungen nötig
```

## 4. Verschlüsselung
```ini
# SSL/TLS erzwingen
require_secure_transport = ON

# Data-at-Rest Encryption
plugin-load-add = file_key_management
file_key_management_filename = /path/to/keyfile
innodb_encrypt_tables = ON
innodb_encrypt_log = ON
```

## 5. Authentifizierung
```sql
-- Unix Socket Plugin für lokale Verbindungen
INSTALL SONAME 'auth_socket';
CREATE USER 'admin'@'localhost' IDENTIFIED VIA unix_socket;
```

## 6. Logging & Audit
```ini
[mysqld]
log_error = /var/log/mysql/error.log
general_log = 1
general_log_file = /var/log/mysql/general.log
slow_query_log = 1

# MariaDB Audit Plugin
plugin-load-add = server_audit
server_audit_logging = ON
```

## 7. Weitere Härtung
- **validate_password Plugin** für Passwort-Policies aktivieren
- **Regelmäßige Updates** einspielen
- **Backups verschlüsselt** speichern
- **Firewall-Regeln** für Port 3306
- **AppArmor/SELinux** Profile nutzen
- **my.cnf Dateiberechtigungen**: 600

## 8. Zu vermeiden
```sql
-- NIEMALS:
GRANT ALL PRIVILEGES ON *.* TO 'user'@'%';
-- Keine leeren Passwörter
-- Keine Standard-Ports in Production
```


