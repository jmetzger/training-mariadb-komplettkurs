# Kill long running process or users 

```
# Session 1
# sleep for 120 seconds 
select sleep(120)

# Session 2
show processlist 
# kill process you have identified for sleep(120) 
MariaDB [(none)]> show processlist;
+----+------+-----------+----------+---------+------+------------+-------------------+----------+
| Id | User | Host      | db       | Command | Time | State      | Info              | Progress |
+----+------+-----------+----------+---------+------+------------+-------------------+----------+
| 36 | root | localhost | NULL     | Query   |    0 | starting   | show processlist  |    0.000 |
| 37 | root | localhost | training | Query   |    4 | User sleep | select sleep(120) |    0.000 |
+----+------+-----------+----------+---------+------+------------+-------------------+----------+
2 rows in set (0.000 sec)
# take 37 
kill 37 

# Session 1: query terminates 
ERROR 2013 (HY000): Lost connection to MySQL server during query

```
