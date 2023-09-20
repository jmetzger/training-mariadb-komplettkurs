# FAQ 

## Wie gehe ich vor, wenn der Slave steht ? 

  * aufgrund eines SQL_Thread - Fehlers
  * d.h. SQL Befehl konnte aufgrund eines Fehlers auf dem Slave nicht ausgeführt werden
  * typisches Beispiel: Duplicate Key 

  * ** Neu aufsetzen **
  * Alles andere wäre brandgefährliches Gefrickel.

```
Sobald der Slave nicht mehr ordnungsgemäß arbeitet
(d.h. der sql_thread steht wg. z.B. Fehlermeldung (Duplicate key), MUSS (best practice) der Slave neu aufgesetzt werden 

Schnellster Weg ist immer mariabackup (non-blocking) 
wenn ihr ausschliesslich innodb-tabellen einsetzt. 
```
