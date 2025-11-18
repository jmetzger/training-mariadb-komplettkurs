# Installation of sakila-db 

```
cd /usr/src
wget https://downloads.mysql.com/docs/sakila-db.tar.gz
tar xvf sakila-db.tar.gz

cd sakila-db 
mariadb < sakila-schema.sql 
mariadb < sakila-data.sql 

```
