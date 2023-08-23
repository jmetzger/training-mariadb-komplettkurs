# Grant / Revoke / Create User 

## Create user 

```
create user training@localhost identified by 'deinpasswort';
```

## Exercise create user 

```
# Als root: 1. Nutzer training anlegen, der sich von lokal anmelden kann 


# 2. ausloggen als root aus mysql -> exit


# 3. anmelden mit nutzer training über mysql-client
# Passwort eingeben 
mysql -utraining -p

# 4. Anschauen, welchen Rechte wir als dieser Nutzer haben
show grants; 

```


## Drop user (=delete user) 

```
drop user training@localhost 
```

## Exercise create external user with privileges 

### Schritt 1 (auf Remote-Server):

```
# Auf dem Remote-System, auf dem der Server läuft (m[1-6].t3isp.de)
# Als root: 1. Nutzer ext anlegen der von überall aus zugreifen darf '%'

```

### Schritt 2 (auf lokalen Server): 

```
# von entfernten System aus, auf dem ein mysql-client existiert (bei uns server1)
# Verbindung aufbauen
mysql -uext -p -h <ip-des-remote-servers-aus-schritt1> 
```

```
-- hier erfahren unsere ip - addresse 
status;
show databases;
show grants;
exit;
```

### Schritt 3 (auf Remote-Server) 

```
-- löschen des Benutzers
drop user ext@'%';

-- neuen Benutzer anlegen mit der IP des lokalen Netzes (aus Schritt 2: status)
# z.B.
create user ext@'<ip-aus-status>' identified by 'meinsupergeheimespasswort'
```

### Schritt 4 (auf lokalen System) 

```
# von entfernten System aus, auf dem ein mysql-client existiert (bei uns server1)
# Verbindung aufbauen
mysql -uext -p -h <ip-des-remote-servers-aus-schritt1> 
```

```
-- hier erfahren unsere ip - addresse 
status;
show databases;
show grants;
exit;
``` 

### Schritt 5 (auf dem RemoteServer): Nutzer alle Rechte aus grant privilegien geben

```
GRANT ALL ON *.* TO ext@'62.91.24.101';
```

### Schritt 6 (auf dem lokalen System): neu verbinden, damit rechte greifen 

```
mysql -uext -p -h <ip-des-remote-servers-aus-schritt1> `
show grants; 
show schemas;
create schema training2;
drop schema training2;
exit;
```

### Schritt 7 (auf dem RemoteServer): Nutzer - Select - Rechte entziehen 

```
revoke select on *.* from ext@'62.81.24.101';
```

### Schritt 6 (auf dem lokalen System): neu verbinden, damit rechte greifen 

```
mysql -uext -p -h <ip-des-remote-servers-aus-schritt1> `
show grants; 
use mysql;
-- should not work 
select user,password from user; 
exit;
```

## Change User (e.g. change authentication) 

```
# change pass
alter user training@localhost identified by 'newpassword';
```

## Set global or db rights for a user 

```
grant all on *.* to training@localhost
# only a specific db 
grant all on mydb.* to training@localhost 
```

## Revoke global or revoke right from a user 

```
revoke select on *.* from training@localhost 
# only from a specific db 
revoke select on training.* from training@localhost 
```

## Refs:

  * https://mariadb.com/kb/en/grant/#the-grant-option-privilege
  * https://mariadb.com/kb/en/revoke/
