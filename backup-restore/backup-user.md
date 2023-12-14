# Backup - User for dumping database with mysqldump 

## General 

  * What are the minimum rights, needed (single-transaction not lock-tables (default))

## Walktrough 

```
CREATE USER backup@localhost identified by 'yoursupersecretepassword';
GRANT SELECT, SHOW VIEW, EVENT, TRIGGER ON sakila.* TO backup@localhost:
```

```
# how to use it
mysqldump -ubackup -p  --single-transaction --routines --events sakila > /usr/src/sakila-db.sql
```
