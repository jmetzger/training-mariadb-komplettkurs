# Exercise innodb_buffer_pool_size 

## Schritt 1: Herausfinden, wie unser innodb_buffer_pool eingestellt ist  ? 

```
mysql
select @@innodb_buffer_pool_size;
show variables like 'innodb%buffer%';
exit
```

## Schritt 2: Arbeitsgröße und Innodb Buffer Pool Size berechnen  

```
# wie gross ist unser Arbeitsspeicher auf dem server
top
# q 
# oder
free
```

```
# berechnen einer guten Größe
# mysql -e 'select <speichergröße>/10 * 8'
mysql -e 'select 3.8/10 * 8'
```

## Schritt 3: innodb_buffer_pool_size in config setzten

```
# Variante 1: Centos /  Redhat 
cd /etc/my.cnf.d/
nano server.cnf 
```

```
# Variante 2:
cd /etc/mysql/mariadb.conf.d
nano 50-service.cnf 
```

```
# unter mysqld - sektion eintrage
innodb-buffer-pool-size=2500M  # 3G
```

```
# server neu starten
systemctl restart mariadb
```

## Schritt 4: Überprüfen und freien Buffer rausfinden 

```
mysql
```

```
-- konfigurierte Größe 
select @@innodb_buffer_pool_size;

-- freien Seiten ermitteln 
show status like '%free%';
show engine innodb status \G

-- verwendete = freie seiten * 16 * 1024
```

