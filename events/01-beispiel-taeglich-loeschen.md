# Event mit Täglich Daten löschen 

## Voraussetzung: 

  * Event das regelmäßig in messages schreibt.

[Vorbereitung Tabelle](/events.md#preparation-1)
[Erstellung Event zum Schreiben](/events.md#recurring-example)

## Übung - zum Daten löschen (Event) 

```
# Beispiele 
# Alles älter als 1 Jahr löschen  
delete FROM messages where createt_at < (now() - interval 2 year)

# alles älter als 10 Minuten
# Er rechnet aber 00:00 Uhr 
delete FROM messages where created_at < (now() - interval 10 minute)
```

```
use schulung;
DELIMITER /
CREATE EVENT eraser_10min
  ON SCHEDULE
    EVERY 10 MINUTE
    STARTS (TIMESTAMP(CURRENT_DATE))
  DO
  BEGIN
  delete FROM messages where created_at < (now() - interval 10 minute);
  END /

DELIMITER ;
```

```
# mach 10 Minuten, z.B. 16:20
# Hat er etwas gelöscht 
select * from messages;
```
