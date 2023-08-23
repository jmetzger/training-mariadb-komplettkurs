# Examples of different scenarios 

## Dump database (data and structure) of sakila and reuse for new database sakilaneu  

  * Why ? Developers need a test database 

```
mysqldump --events --routines sakila > /usr/src/all-sakila.sql
# Datenbank erstellen
mysql -e "create schema sakilaneu;"
mysql sakilaneu < /usr/src/all-sakila.sql 
```

## Only dump structure of database sakila 

```
mysqldump --events --routines --no-data sakila > /usr/src/structure-sakila.sql
```

## Only data / no create of database sakila and table actor 

```
mysqldump --no-create-info sakila actor > /usr/src/sakila-actor-data.sql
```
