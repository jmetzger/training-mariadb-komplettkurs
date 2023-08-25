# procedure 

```
use sakila;
DELIMITER //
```

```
CREATE PROCEDURE simpleproc (OUT param1 INT)
BEGIN
  SELECT COUNT(*) INTO param1 FROM actor;
END;
//
```

```
DELIMITER ;
```

```
CALL simpleproc(@a);
```

```
SELECT @a;
```

```
+------+
| @a   |
+------+
|  200 |
+------+
```
