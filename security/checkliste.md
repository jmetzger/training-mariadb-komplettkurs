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

## 4. Verschlüsselung (Tabellen - encryption - on - rest)
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


Perfekt, dann machen wir das jetzt einmal „konkret für 11.8 auf einer Single-VM“.
Ich gehe davon aus: klassische Linux-VM, MariaDB als Paket installiert.

---

## 1. Beispiel-Config (`my.cnf`) für MariaDB 11.8 (Single-VM)

Je nach Distribution liegt die Server-Config z. B. in:

* `/etc/my.cnf` **oder**
* `/etc/my.cnf.d/50-server.cnf` / `/etc/mysql/mariadb.conf.d/50-server.cnf`

### Vorschlag: sicherheitsfokussiertes Grund-Setup

```ini
[mysqld]
#######################################
# Netzwerk / Zugriff
#######################################

# Nur lokale Verbindungen erlauben (wenn App auf derselben VM läuft)
bind-address = 127.0.0.1

# Standard-Port
port = 3306

# Kein Port-Scan-Noise im Internet
skip-name-resolve

#######################################
# Sicherheit
#######################################

# Verhindert LOAD DATA LOCAL INFILE (SQL-Injection-Angriffspfad)
local-infile = 0

# Nur dieses Verzeichnis für Import/Export nutzen
secure-file-priv = /var/lib/mysql-files

# Standard-Auth (je nach Setup anpassbar, Beispiel):
# Ab MariaDB 10.4 ist unix_socket oft Standard für 'root'@'localhost'.
# Hier optional ein stärkeres Plugin als Beispiel:
# default_authentication_plugin = ed25519

#######################################
# SSL / Verschlüsselung (wenn remote-Verbindungen)
#######################################
# Wenn du TLS nutzt, auskommentieren und Pfade anpassen:
# ssl = on
# ssl-cert = /etc/mysql/ssl/server-cert.pem
# ssl-key  = /etc/mysql/ssl/server-key.pem
# ssl-ca   = /etc/mysql/ssl/ca.pem

#######################################
# Logging
#######################################

log_error = /var/log/mysql/error.log

# Langsame Queries protokollieren (Performance + ggf. Security-Auswertung)
slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow.log
long_query_time = 1

#######################################
# Schutz gegen DoS / zu viele Verbindungen
#######################################

max_connections  = 200
connect_timeout  = 10
wait_timeout     = 600
interactive_timeout = 600

#######################################
# InnoDB / Storage (nur Auszug)
#######################################

innodb_file_per_table = 1

# Optionale (Version abhängig!) Encryption-Flags:
# innodb_encrypt_tables = ON
# innodb_encrypt_log    = ON
# encrypt_binlog        = ON

#######################################
# Sonstiges
#######################################

# Explizite SQL-Mode-Einstellungen (optional)
# sql_mode = STRICT_ALL_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
```

**Wichtig:**

* Wenn du **remote von einer App-VM** zugreifen willst, ändere `bind-address` auf
  `0.0.0.0` **und** schränke den Zugriff strikt über Firewall + TLS + User/Host ein.
* `secure-file-priv`-Verzeichnis muss existieren:

  ```bash
  sudo mkdir -p /var/lib/mysql-files
  sudo chown mysql:mysql /var/lib/mysql-files
  ```

---

## 2. Einmalige SQL-Härtung nach der Installation

Verbinde dich lokal als root:

```bash
mysql -u root -p
```

### 2.1. Anonyme User entfernen

```sql
SELECT user, host FROM mysql.user WHERE user = '';

-- Falls vorhanden:
DROP USER ''@'localhost';
DROP USER ''@'%';
FLUSH PRIVILEGES;
```

### 2.2. Root nur lokal erlauben

Check:

```sql
SELECT user, host FROM mysql.user WHERE user='root';
```

Wenn da z. B. `root@'%'` oder `root@'hostname'` steht, kannst du das härten:

```sql
-- Beispiel: alles auf localhost einschränken
UPDATE mysql.user
   SET host='localhost'
 WHERE user='root'
   AND host <> 'localhost';

FLUSH PRIVILEGES;
```

> Ziel: Nur `root@localhost` bleibt übrig.

### 2.3. Starke Passwörter + Passwort-Check-Plugin

Abhängig von deiner Paketierung, z. B. `simple_password_check`:

```sql
INSTALL SONAME 'simple_password_check';

SET GLOBAL simple_password_check_minimal_length = 12;
SET GLOBAL simple_password_check_digits         = 1;
SET GLOBAL simple_password_check_letters        = 1;
SET GLOBAL simple_password_check_other          = 1;
```

Optional kannst du das in der Config festhalten:

```ini
[mysqld]
simple_password_check_minimal_length = 12
simple_password_check_digits         = 1
simple_password_check_letters        = 1
simple_password_check_other          = 1
```

---

## 3. Saubere User für deine App (Least Privilege)

Angenommen, du hast eine DB `appdb` und eine App auf derselben VM:

```sql
CREATE DATABASE appdb
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

CREATE USER 'appuser'@'localhost'
  IDENTIFIED BY 'SehrSicheres!Passwort123!';

GRANT SELECT, INSERT, UPDATE, DELETE
  ON appdb.* TO 'appuser'@'localhost';

FLUSH PRIVILEGES;
```

Wenn die App **remote** zugreift (z. B. von 10.0.10.x):

```sql
CREATE USER 'appuser'@'10.0.%'
  IDENTIFIED BY 'SehrSicheres!Passwort123!'
  REQUIRE SSL;

GRANT SELECT, INSERT, UPDATE, DELETE
  ON appdb.* TO 'appuser'@'10.0.%';

FLUSH PRIVILEGES;
```

Dazu musst du natürlich TLS in `my.cnf` aktiv haben.

---

## 4. TLS (Kurz-Variante)

Wenn du remote arbeitest:

1. Zertifikate erzeugen (z. B. via `mysql_ssl_rsa_setup` oder Openssl).
2. In `my.cnf` eintragen (siehe oben).
3. User mit `REQUIRE SSL` definieren (siehe Beispiel).

Kontrolle:

```sql
SHOW VARIABLES LIKE 'have_ssl';
SHOW STATUS LIKE 'Ssl_cipher';
```

Bei einer aktiven TLS-Verbindung sollte `Ssl_cipher` nicht leer sein.

---

## 5. Backups und Rechte

Auf einer Single-VM wirst du oft `mariabackup` oder `mysqldump` nutzen.

**Beispiel cron.daily-Backup (mysqldump, sehr einfach):**

```bash
#!/bin/bash
BACKUP_DIR=/var/backups/mariadb
mkdir -p "$BACKUP_DIR"

mysqldump --all-databases \
  --single-transaction --quick --routines \
  | gzip > "$BACKUP_DIR/all-dbs-$(date +%F).sql.gz"

find "$BACKUP_DIR" -type f -mtime +14 -delete
```

* Script z. B. als `/etc/cron.daily/mariadb-backup`
* Rechte einschränken:

  ```bash
  chown root:root /etc/cron.daily/mariadb-backup
  chmod 700 /etc/cron.daily/mariadb-backup
  ```

Optional: Backup-Verzeichnis auf ein **verschlüsseltes Volume** legen oder Dateien mit GPG verschlüsseln.

---

## 6. Kleine Checkliste speziell für deine Single-VM

Für MariaDB 11.8 Single-VM würde ich konkret prüfen:

1. `bind-address = 127.0.0.1` (falls App auf der gleichen VM läuft).
2. `local-infile = 0` und `secure-file-priv` gesetzt.
3. Keine anonymen User, root nur `root@localhost`.
4. Passwort-Check-Plugin aktiviert + starke Passwörter.
5. App-User nur mit minimalen Rechten, **kein** `GRANT ALL ON *.*`.
6. Logging aktiv (`error.log`, `slow_query_log`).
7. Sinnvolle Limits (`max_connections`, Timeouts).
8. Backup-Script + Restore-Test.

