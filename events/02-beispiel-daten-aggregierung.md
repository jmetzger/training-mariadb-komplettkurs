# Daten aggregieren mit Events 

## Schritt 1:

```
 use sakila; select first_name from actor;
```

## Schritt 2: Gruppieren nach Vorname 

```
select first_name from actor group by first_name;
```

## Schritt 3: auch noch die Anzahl ausgeben 

```
select first_name,count(*) as namenzahl from actor group by first_name;
```

## Schritt 4: nach buchstaben 

```
select LEFT(first_name,1),count(*) as namenzahl from actor group by LEFT(first_name,1);
```

## Schritt 5: Tabellenstruktur anlegen 

```
CREATE TABLE actor_stats (    first_letter CHAR(1),    actor_count INT,    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);
```

## Schritt 6: Testweise Daten einfügen 

```
INSERT INTO actor_stats (first_letter, actor_count)
  SELECT LEFT(first_name,1),count(*) as namenzahl 
  FROM actor 
  GROUP BY LEFT(first_name,1);

select * from actor_stats; 
truncate actor_stats;
select * from actor_stats; 
```

## Schritt 7: Procedure 

```
DELIMITER $$
CREATE PROCEDURE aggregate_actors_by_letter()
BEGIN
  TRUNCATE actor_stats;
  INSERT INTO actor_stats (first_letter, actor_count)
  SELECT LEFT(first_name,1),count(*) as namenzahl 
  FROM actor 
  GROUP BY LEFT(first_name,1);  
  
END$$
DELIMITER ;
```

## Schritt 8: Testweise aufrufen 

```
 CALL aggregate_actors_by_letter;
```

## Schritt 9: event

```
DELIMITER /
CREATE EVENT actor_stats
ON SCHEDULE EVERY 1 MINUTE
DO
   BEGIN
     CALL aggregate_actors_by_letter();
   END /

-- Delimiter wieder auf ';' setzen 
DELIMITER ;

show events; 
TRUNCATE actor_stats;
```
--  nach 1 minute 
SELECT * FROM actor_stats;
