# Grant / Revoke / Create User 

## Create user 

```
create user training@localhost identified by 'deinpasswort';
```

## Exercise 1: create user 

### In Session 1: (mysql - user - root) 

```
# Als root: 1. Nutzer training anlegen, der sich von lokal anmelden kann 
create user training@localhost identified by 'deinpasswort';
# Wir zeigen uns die Rechte an:
SHOW GRANTS FOR training@localhost;
```

### In Session 2: 

```
# anmelden mit nutzer training über mysql-client
# Passwort eingeben 
mysql -utraining -p
```

```
# 4. Anschauen, welchen Rechte wir als dieser Nutzer haben
show grants; 
show databases;
use sakila; 
```

## Exercise 1a: privileges anpassen / alle Rechte 

## In Session 1: mysql -> root 

```
GRANT ALL ON *.* TO training@localhost;
show grants for training@localhost;
```

## In Session 2: mysql -> training 

```
# das geht noch nicht 
create schema planung;
exit;
```

```
mysql -utraining -p
```

```
# jetzt geht es
create schema planung;
```

## Exercise 1b: privileges anpassen / nur SELECT  

## In Session 1: mysql -> root 

```
REVOKE ALL ON *.* FROM training@localhost;
show grants for training@localhost;

GRANT SELECT ON *.* TO training@localhost;
show grants for training@localhost;
```

## In Session 2: mysql -> training 

```
use sakila;
# should not work but does work 
update actor set first_name = 'johanna' where actor_id = 1;
exit;
```

```
mysql -utraining -p
```

```
# jetzt geht es nicht mehr 
update actor set first_name = 'johanna' where actor_id = 1;
# aber das geht
select * from actor where actor_id = 1;
```

## Exercise 1c: Drop user (=delete user) 

```
# as user root 
drop user training@localhost 
```

## Exercise 2: create external user with privileges 

### Schritt 1 (auf Remote-Server):

```
Variante 1:
# Auf dem Remote-System, auf dem der Server läuft (m[1-6].t3isp.de)
# Als root: 1. Nutzer ext anlegen der von überall aus zugreifen darf '%'

```

```
Variante 2:
create user ext@'192.168.56.%' identified by 'password';


```

### Schritt 2 (auf lokalen Server): 

```
# von entfernten System aus, auf dem ein mysql-client existiert (bei uns server1)
# Verbindung aufbauen
mysql -uext -p -h <ip-des-remote-servers>
```

```
-- hier erfahren unsere ip - addresse 
status;
show databases;
show grants;
exit;
```

### Schritt 3: Remote-Server Set db rights for a user 

```
grant all on sakila.* to ext@'192.168.56.%';
```

### Schritt 4: Local System 

```
exit;
# on local system test connection
mysql -uext -p -h<ip des remoteserver>
show grants;
show databases;
exit;
```

## Refs:

  * https://mariadb.com/kb/en/grant/#the-grant-option-privilege
  * https://mariadb.com/kb/en/revoke/
