# Events with examples 

## Preparation 

```
-- scheduler is not there 
SHOW PROCESSLIST;

-- Prüfen ob scheduler läuft 
show variables like '%event%';
set GLOBAL event_scheduler = on; 

-- scheduler appears 
SHOW PROCESSLIST;

-- Events anzeigen 
show events; 
```

## preparation  

```

create schema if not exists schulung;
USE schulung;
CREATE TABLE messages (
    id INT PRIMARY KEY AUTO_INCREMENT,
    message VARCHAR(255) NOT NULL,
    created_at DATETIME NOT NULL
);
```

## One time event 

```
USE schulung;
CREATE EVENT IF NOT EXISTS test_event_01
ON SCHEDULE AT CURRENT_TIMESTAMP
DO
  INSERT INTO messages(message,created_at)
  VALUES('Test MariaDB Event 1',NOW());
  
SELECT * FROM messages;  
  
```

## Show all events from a specific database 

```



SHOW EVENTS FROM schulung;
```

## Show all events in active database 

```
USE schulung;
SHOW EVENTS;

```

## One time event but preserved (so runs once every minute) 

```
To keep the event after it is expired, you use the  ON COMPLETION PRESERVE clause.

CREATE EVENT test_event_02
ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 MINUTE
ON COMPLETION PRESERVE
DO
   INSERT INTO messages(message,created_at)
   VALUES('Test MariaDB Event 2',NOW());





```

## Same version, but with begin end block 

```
DELIMITER /
CREATE EVENT test_event_03
ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 MINUTE
ON COMPLETION PRESERVE
DO
   BEGIN
   INSERT INTO messages(message,created_at)
   VALUES('Test MariaDB Event 3',NOW());
   END /
DELIMITER ;

SELECT * FROM messages;

```

## Recurring Example 

```
CREATE EVENT test_event_03
ON SCHEDULE EVERY 1 MINUTE
STARTS CURRENT_TIMESTAMP
ENDS CURRENT_TIMESTAMP + INTERVAL 1 HOUR
DO
   INSERT INTO messages(message,created_at)
   VALUES('Test MariaDB recurring Event',NOW());

SELECT * FROM messages;

// after 1 minute 
SELECT * FROM messages;


```

## Drop an event 

```
DROP EVENT IF EXIST test_event_03;
```


## Set event-scheduler in config / my.cnf / my.ini

```
[mysqld]
event-scheduler

# after that restawrt 
systemctl restart mariadb 
```

## Fix timezone problem Linux (when time is displayed wrong) 

```
# 09:32 UTC should be 11:32 CEST 
# also root ausführen 
timedatectl list-timezones  | grep 'Europe/Berlin';
timedatectl set-timezone Europe/Berlin
timedatectl
date
systemctl restart mariadb 
mysql
mysql>select now();
mysql>--- time should ok now 
```

## Exercise 

### Step 1: Events einschalten 

```
-- scheduler is not there 
SHOW PROCESSLIST;

-- Prüfen ob scheduler läuft 
show variables like '%event%';
set GLOBAL event_scheduler = on; 

-- scheduler appears 
SHOW PROCESSLIST;

-- Events anzeigen 
show events; 
```

### Step 2: create messages for test 


```
create schema if not exists schulung;
USE schulung;
CREATE TABLE messages (
    id INT PRIMARY KEY AUTO_INCREMENT,
    message VARCHAR(255) NOT NULL,
    created_at DATETIME NOT NULL
);
```

### Step 3: create recurring event

```
use schulung; 
CREATE EVENT test_event_03
ON SCHEDULE EVERY 1 MINUTE
STARTS CURRENT_TIMESTAMP
ENDS CURRENT_TIMESTAMP + INTERVAL 1 HOUR
DO
   INSERT INTO messages(message,created_at)
   VALUES('Test MariaDB recurring Event',NOW());

show events;
```

```
use schulung;
SELECT * FROM messages;
```

```
use schulung; 
-- after 1 minute 
SELECT * FROM messages;


```
