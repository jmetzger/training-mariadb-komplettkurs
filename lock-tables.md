# Lock tables - Example 

## Example 

```
mysql> LOCK TABLES people write,people_data write;
Query OK, 0 rows affected (0.00 sec)

mysql> UNLOCK TABLES
    -> ;
Query OK, 0 rows affected (0.00 sec)


# LOCK TABLES .... WRITE 
-- We cannot read + write in other session  

# LOCK TABLES ..... READ 
-- We cannot write, but read in other session 


```

## Exercise 

### Vorbereitung:

  * 2 Session die als root mit mysql - client am Server eingeloggt sind 

### Step 1: Session 1 

```
use sakila;
LOCK TABLES actor write;
```

### Step 2: Session 2 

```
use sakila;
update actor set last_name = 'CHAMPION' where actor_id = 200;
----
```

### Step 3: Session 1

```
UNLOCK TABLES
-- now update in Step2 should work
```
