# Views and performance 

## General  

```
SHOW CREATE VIEW
```

## Views and Algorithms

   * Views can use 3 algorithms:
     * merge 
     * simple rewrites (translates the query)  
   * temptable 
     * Creates a temptable to retrieve information
     * (In this case no indexes can be used - was the case in older versions) 
   * Shows up in explain with derived
   * undefined 
     * MySQL chooses, if to use merge or temptable 
     * prefers merge over temptable if possible 

## Example 

```
+----+-------------+------------+------+---------------+------+---------+------+------+-------+
| id | select_type | table      | type | possible_keys | key  | key_len | ref  | rows | Extra |
+----+-------------+------------+------+---------------+------+---------+------+------+-------+
|  1 | PRIMARY     | <derived2> | ALL  | NULL          | NULL | NULL    | NULL |   33 | NULL  |
|  2 | DERIVED     | task       | ALL  | NULL          | NULL | NULL    | NULL |   33 | NULL  |
+----+-------------+------------+------+---------------+------+---------+------+------+-------+
```
      

## Handling (best practice)

   * You can define the algorithm when creating the view 
   * If you define merge and mysql cannot handle it
     * you will get a warning 


## Example of handling 

```
mysql> CREATE ALGORITHM=MERGE VIEW priority_counts AS SELECT priority_id, COUNT(1) AS quanity FROM task GROUP BY priority_id;
Query OK, 0 rows affected, 1 warning (0.12 sec)

mysql> SHOW WARNINGS;
+---------+------+-------------------------------------------------------------------------------+
| Level   | Code | Message                                                                       |
+---------+------+-------------------------------------------------------------------------------+
| Warning | 1354 | View merge algorithm can't be used here for now (assumed undefined algorithm) |
+---------+------+-------------------------------------------------------------------------------+
1 row in set (0.08 sec)
```
   
## Reference

   * Ref: https://dba.stackexchange.com/questions/54481/determining-what-algorithm-mysql-view-is-using
 
