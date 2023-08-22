# Set global Server System variable 

## Find out current value 

```
# show global variable 
show global variables like '%automatic_sp%'
# or // variable_name needs to be in captitals 
use information_schema
select * from global_variables where variable_name like '%AUTOMATIC_SP%';

# If you know the exact name 
select @@global.automatic_sp_privileges;
select @@GLOBAL.automatic_sp_privileges;

# Find out session variable, if you know exact name
select @@automatic_sp_privileges;

```

## Set global Variable 

```
# will be set like so till next restart of mysql server 
set global automatic_sp_privileges = 0 
```

## automatic_sp_privileges can only be set globally 

```
# Refer to: server system variable doku 

# Has same value in global an session scope 
MariaDB [information_schema]> select @@automatic_sp_privileges; select @@global.automatic_sp_privileges;
+---------------------------+
| @@automatic_sp_privileges |
+---------------------------+
|                         0 |
+---------------------------+
1 row in set (0.000 sec)

+----------------------------------+
| @@global.automatic_sp_privileges |
+----------------------------------+
|                                0 |
+----------------------------------+
1 row in set (0.000 sec)
```

## Reference:

  * https://mariadb.com/kb/en/server-system-variables/#automatic_sp_privileges
