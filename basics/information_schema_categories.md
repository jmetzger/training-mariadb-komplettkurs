# information_schema - Kategorien 

```
3.1.
a. Metadaten - Tabellen:  SCHEMATA, TABLES. COLUMNS 

3.2.
b. Status und Variablen - Tabellen: 
GLOBAL_VARIABLES, SESSION_VARIABLES 
GLOBAL_STATUS, SESSION_STATUS 

3.3. Privilege Tables: 

Alles was mit _PRIVILEGES endet.

3.4. Profiling-Tabelle 

Execution time messen

3.5. Processlist - Tabelle 

3.6. Bereich 

3.6.1. InnoDB locks tabellen 

INNODB_LOCKS 
INNODB_LOCK_WAITS 
INNODB_TRX 

aktive locks, waits and transaction, die 
einen lock aquiriert haben oder auf einen lock warten

3.6.2. Innodb buffer pool tables 

Alle Tabellen mit INNODB_BUFFER 
sind buffer pool inhalts und page usage

3.6.3. INNODB_METRICS

Statistiken über low level operationen 

3.6.4. InnoDB compression Tables.

Alle INNODB_CMP 
stellt informationen zu der performance
von komprimierte "pages" in innodb bereit 

3.6.5. InnoDB full-text - Tabellen 

INNODB_FT_ 
Informationüber Fulltext Indexes in InnoDB 

3.6.6 Innodb data dictionary 

Tabellen die mit INNODB_SYS_ starten
halten metadaten über InnoDB tabellen, columns
und foreign keys bereit

Sie sind wie die normalen Tabellen zu den Metadaten,
aber mehr innodb spezifisch 


```
