# Multi-Instances 

## Übung: Mehrere MariaDB-Instanzen unter Rocky Linux (mit SELinux)

**Ziel:**

* 2. Instanz `mariadb@node1.service`
* eigenes Datadir
* eigene Config
* SELinux-konform
* systemd-Template nutzen

Rocky Linux verwendet SELinux (default **Enforcing**) → wenn du das nicht berücksichtigst, startet die zweite Instanz NICHT.

---

## 0. Voraussetzungen

```bash
sudo systemctl status mariadb
systemctl list-unit-files | grep mariadb@
```

Template sollte aussehen wie:

```
mariadb@.service  enabled
```

## 1. Einmalig für alle Instanzen (template) 

```
systemctl edit mariadb@.service
```

```
[Service]
ProtectHome=false
Environment='MYSQLD_MULTI_INSTANCE=--defaults-file=/etc/my%I.cnf \
                        --socket=/var/run/mysqld/mysqld-%I.sock \
                        --datadir=/var/lib/mysql-%I \
                        --skip-networking'
```



## 1. Eigenes Datadir anlegen (SELinux-konform!)

MariaDB verwendet Standard-Label:

* Datenverzeichnisse → `mysqld_db_t`
* Laufzeitdateien (Sockets) → `mysqld_var_run_t`

Wir erstellen:

```bash
sudo mkdir -p /var/lib/mysql-node1
sudo chown -R mysql:mysql /var/lib/mysql-node1
```

Jetzt SELinux richtig labeln:

```bash
sudo semanage fcontext -a -t mysqld_db_t "/var/lib/mysql-node1(/.*)?"
sudo restorecon -Rv /var/lib/mysql-node1
```

**Wichtig:**
Ohne dieses Label startet die Instanz NICHT.

---

## 2. Runtime-Pfad für Socket vorbereiten (SELinux)

Rocky nutzt idR `/var/run/mysqld`.
Wir erstellen einen separaten Socket-Namen für node1:

```bash
sudo mkdir /var/run/mysqld
chown mysql:mysql /var/run/mysqld
sudo touch /var/run/mysqld/mysqld-node1.sock
sudo chown mysql:mysql /var/run/mysqld/mysqld-node1.sock
sudo semanage fcontext -a -t mysqld_var_run_t "/var/run/mysqld/mysqld-node1.sock"
sudo restorecon -v /var/run/mysqld/mysqld-node1.sock
```

(Das echte Socket wird später vom Daemon erstellt → aber das Label muss vorher existieren.)

---

## 3. Konfigurationsdatei für die neue Instanz anlegen

Auf RHEL/Rocky liegen instanz-spezifische Dateien standardmäßig unter:

Wir erstellen:

```bash
sudo tee /etc/mynode1.cnf >/dev/null <<'EOF'
[mariadbd]
# Eigenes Datadir
datadir=/var/lib/mysql-node1

# Eigener Socket
socket=/var/run/mysqld/mysqld-node1.sock

# Eigener Port
port=3307

# Eigener PID-File
pid-file=/var/run/mysqld/mysqld-node1.pid
EOF
```

```bash
sudo semanage fcontext -a -t mysqld_etc_t "/etc/mynode1.cnf"
sudo restorecon -v /etc/mynode1.cnf

```

---

## 4. Instanz starten

```bash
sudo systemctl start mariadb@node1.service
sudo systemctl status mariadb@node1.service
```

Wenn SELinux etwas blockiert hätte, würdest du es sehen mit:

```bash
sudo journalctl -u mariadb@node1.service -xe
sudo grep AVC /var/log/audit/audit.log
```

---

## 5. Mit der neuen Instanz verbinden

```bash
sudo mysql --socket=/var/run/mysqld/mysqld-node1.sock -u root
```

Testbefehle:

```sql
SHOW VARIABLES LIKE 'port';
CREATE DATABASE multi_instance_test;
SHOW DATABASES;
EXIT;
```

---

## 6. SELinux Troubleshooting (falls nötig)

Sollte trotzdem ein AVC auftauchen, hilf dir selbst:

### A) Zeige die letzten SELinux-Fehler

```bash
sudo ausearch -m avc -ts recent
```

### B) Automatische Policy generieren (Notlösung!)

```bash
sudo grep mariadbd /var/log/audit/audit.log | audit2allow -M mariadb_multi
sudo semodule -i mariadb_multi.pp
```

---

## 7. Instanz stoppen / Boot-Start aktivieren

```bash
sudo systemctl stop mariadb@node1.service
sudo systemctl start mariadb@node1.service
sudo systemctl enable mariadb@node1.service
```

## Reference: 

  * https://mariadb.com/docs/server/server-management/starting-and-stopping-mariadb/systemd#interacting-with-multiple-mariadb-server-processes

