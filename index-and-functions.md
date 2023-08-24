# Index and functions 

## No function can be used on an index:

```
explain select * from actor where substring(last_name,1,1) = 'A';
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | actor | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  200 |   100.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

## Workaround with generated columns 

```
# 1. Create Virtual Column with upper 
MariaDB [sakila]> alter table actor add last_name_upper varchar(45) AS (upper(last_name)) VIRTUAL;
Query OK, 0 rows affected (0.006 sec)
Records: 0  Duplicates: 0  Warnings: 0

MariaDB [sakila]> create index idx_upper on actor (last_name_upper);
Query OK, 0 rows affected (0.008 sec)
Records: 0  Duplicates: 0  Warnings: 0

MariaDB [sakila]> explain select * from actor where last_name_upper like 'A%';                
+------+-------------+-------+-------+---------------+-----------+---------+------+------+-------------+
| id   | select_type | table | type  | possible_keys | key       | key_len | ref  | rows | Extra       |
+------+-------------+-------+-------+---------------+-----------+---------+------+------+-------------+
|    1 | SIMPLE      | actor | range | idx_upper     | idx_upper | 183     | NULL |    7 | Using where |
+------+-------------+-------+-------+---------------+-----------+---------+------+------+-------------+
1 row in set (0.001 sec)
```
  
## Now we try to search the very same 

```
explain select * from actor where last_name_upper like 'A%';
+----+-------------+-------+------------+-------+---------------------+---------------------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type  | possible_keys       | key                 | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+-------+---------------------+---------------------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | actor | NULL       | range | idx_last_name_upper | idx_last_name_upper | 183     | NULL |    7 |   100.00 | Using where |
+----+-------------+-------+------------+-------+---------------------+---------------------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

```

## Reference 

  * https://mariadb.com/kb/en/generated-columns/
  * https://mariadb.com/kb/en/slow-query-log-overview/
