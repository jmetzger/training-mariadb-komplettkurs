# Bis zu welcher Größe kann man mariadb einsetzen ? 

## Lastprofil (InnoDB) 

  * 20% Schreiben und 80% Lesen

## Datengrenze (Empfehlung) bei CPU gebundener Last 

  * Häufig verwendete Daten müssen in den innodb_buffer_pool passen
  * BeispieL. Häufige Nutzdaten sind z.B. 200GB, die gesamten 2 TB

## Tablespaces - Begrenzung aufgrund der Page - Größe:

```
Speziell: 
bei 16 KByte Pages - Max 64 TB pro Tablespace.
oder maximal 1017 columns 
```

## Maximale Row-Länge bei Verwendung von InnoDB 

  * 50% der Page-Größe -> 16 Kbytes -> 8 Kbytes 

### Exercise 

```
mysql
```

```
create schema if not exists training; 
use training;

SET GLOBAL innodb_default_row_format='dynamic';

SET SESSION innodb_strict_mode=ON;

CREATE OR REPLACE TABLE tab (
   col1 varchar(40) NOT NULL,
   col2 varchar(40) NOT NULL,
   col3 varchar(40) NOT NULL,
   col4 varchar(40) NOT NULL,
   col5 varchar(40) NOT NULL,
   col6 varchar(40) NOT NULL,
   col7 varchar(40) NOT NULL,
   col8 varchar(40) NOT NULL,
   col9 varchar(40) NOT NULL,
   col10 varchar(40) NOT NULL,
   col11 varchar(40) NOT NULL,
   col12 varchar(40) NOT NULL,
   col13 varchar(40) NOT NULL,
   col14 varchar(40) NOT NULL,
   col15 varchar(40) NOT NULL,
   col16 varchar(40) NOT NULL,
   col17 varchar(40) NOT NULL,
   col18 varchar(40) NOT NULL,
   col19 varchar(40) NOT NULL,
   col20 varchar(40) NOT NULL,
   col21 varchar(40) NOT NULL,
   col22 varchar(40) NOT NULL,
   col23 varchar(40) NOT NULL,
   col24 varchar(40) NOT NULL,
   col25 varchar(40) NOT NULL,
   col26 varchar(40) NOT NULL,
   col27 varchar(40) NOT NULL,
   col28 varchar(40) NOT NULL,
   col29 varchar(40) NOT NULL,
   col30 varchar(40) NOT NULL,
   col31 varchar(40) NOT NULL,
   col32 varchar(40) NOT NULL,
   col33 varchar(40) NOT NULL,
   col34 varchar(40) NOT NULL,
   col35 varchar(40) NOT NULL,
   col36 varchar(40) NOT NULL,
   col37 varchar(40) NOT NULL,
   col38 varchar(40) NOT NULL,
   col39 varchar(40) NOT NULL,
   col40 varchar(40) NOT NULL,
   col41 varchar(40) NOT NULL,
   col42 varchar(40) NOT NULL,
   col43 varchar(40) NOT NULL,
   col44 varchar(40) NOT NULL,
   col45 varchar(40) NOT NULL,
   col46 varchar(40) NOT NULL,
   col47 varchar(40) NOT NULL,
   col48 varchar(40) NOT NULL,
   col49 varchar(40) NOT NULL,
   col50 varchar(40) NOT NULL,
   col51 varchar(40) NOT NULL,
   col52 varchar(40) NOT NULL,
   col53 varchar(40) NOT NULL,
   col54 varchar(40) NOT NULL,
   col55 varchar(40) NOT NULL,
   col56 varchar(40) NOT NULL,
   col57 varchar(40) NOT NULL,
   col58 varchar(40) NOT NULL,
   col59 varchar(40) NOT NULL,
   col60 varchar(40) NOT NULL,
   col61 varchar(40) NOT NULL,
   col62 varchar(40) NOT NULL,
   col63 varchar(40) NOT NULL,
   col64 varchar(40) NOT NULL,
   col65 varchar(40) NOT NULL,
   col66 varchar(40) NOT NULL,
   col67 varchar(40) NOT NULL,
   col68 varchar(40) NOT NULL,
   col69 varchar(40) NOT NULL,
   col70 varchar(40) NOT NULL,
   col71 varchar(40) NOT NULL,
   col72 varchar(40) NOT NULL,
   col73 varchar(40) NOT NULL,
   col74 varchar(40) NOT NULL,
   col75 varchar(40) NOT NULL,
   col76 varchar(40) NOT NULL,
   col77 varchar(40) NOT NULL,
   col78 varchar(40) NOT NULL,
   col79 varchar(40) NOT NULL,
   col80 varchar(40) NOT NULL,
   col81 varchar(40) NOT NULL,
   col82 varchar(40) NOT NULL,
   col83 varchar(40) NOT NULL,
   col84 varchar(40) NOT NULL,
   col85 varchar(40) NOT NULL,
   col86 varchar(40) NOT NULL,
   col87 varchar(40) NOT NULL,
   col88 varchar(40) NOT NULL,
   col89 varchar(40) NOT NULL,
   col90 varchar(40) NOT NULL,
   col91 varchar(40) NOT NULL,
   col92 varchar(40) NOT NULL,
   col93 varchar(40) NOT NULL,
   col94 varchar(40) NOT NULL,
   col95 varchar(40) NOT NULL,
   col96 varchar(40) NOT NULL,
   col97 varchar(40) NOT NULL,
   col98 varchar(40) NOT NULL,
   col99 varchar(40) NOT NULL,
   col100 varchar(40) NOT NULL,
   col101 varchar(40) NOT NULL,
   col102 varchar(40) NOT NULL,
   col103 varchar(40) NOT NULL,
   col104 varchar(40) NOT NULL,
   col105 varchar(40) NOT NULL,
   col106 varchar(40) NOT NULL,
   col107 varchar(40) NOT NULL,
   col108 varchar(40) NOT NULL,
   col109 varchar(40) NOT NULL,
   col110 varchar(40) NOT NULL,
   col111 varchar(40) NOT NULL,
   col112 varchar(40) NOT NULL,
   col113 varchar(40) NOT NULL,
   col114 varchar(40) NOT NULL,
   col115 varchar(40) NOT NULL,
   col116 varchar(40) NOT NULL,
   col117 varchar(40) NOT NULL,
   col118 varchar(40) NOT NULL,
   col119 varchar(40) NOT NULL,
   col120 varchar(40) NOT NULL,
   col121 varchar(40) NOT NULL,
   col122 varchar(40) NOT NULL,
   col123 varchar(40) NOT NULL,
   col124 varchar(40) NOT NULL,
   col125 varchar(40) NOT NULL,
   col126 varchar(40) NOT NULL,
   col127 varchar(40) NOT NULL,
   col128 varchar(40) NOT NULL,
   col129 varchar(40) NOT NULL,
   col130 varchar(40) NOT NULL,
   col131 varchar(40) NOT NULL,
   col132 varchar(40) NOT NULL,
   col133 varchar(40) NOT NULL,
   col134 varchar(40) NOT NULL,
   col135 varchar(40) NOT NULL,
   col136 varchar(40) NOT NULL,
   col137 varchar(40) NOT NULL,
   col138 varchar(40) NOT NULL,
   col139 varchar(40) NOT NULL,
   col140 varchar(40) NOT NULL,
   col141 varchar(40) NOT NULL,
   col142 varchar(40) NOT NULL,
   col143 varchar(40) NOT NULL,
   col144 varchar(40) NOT NULL,
   col145 varchar(40) NOT NULL,
   col146 varchar(40) NOT NULL,
   col147 varchar(40) NOT NULL,
   col148 varchar(40) NOT NULL,
   col149 varchar(40) NOT NULL,
   col150 varchar(40) NOT NULL,
   col151 varchar(40) NOT NULL,
   col152 varchar(40) NOT NULL,
   col153 varchar(40) NOT NULL,
   col154 varchar(40) NOT NULL,
   col155 varchar(40) NOT NULL,
   col156 varchar(40) NOT NULL,
   col157 varchar(40) NOT NULL,
   col158 varchar(40) NOT NULL,
   col159 varchar(40) NOT NULL,
   col160 varchar(40) NOT NULL,
   col161 varchar(40) NOT NULL,
   col162 varchar(40) NOT NULL,
   col163 varchar(40) NOT NULL,
   col164 varchar(40) NOT NULL,
   col165 varchar(40) NOT NULL,
   col166 varchar(40) NOT NULL,
   col167 varchar(40) NOT NULL,
   col168 varchar(40) NOT NULL,
   col169 varchar(40) NOT NULL,
   col170 varchar(40) NOT NULL,
   col171 varchar(40) NOT NULL,
   col172 varchar(40) NOT NULL,
   col173 varchar(40) NOT NULL,
   col174 varchar(40) NOT NULL,
   col175 varchar(40) NOT NULL,
   col176 varchar(40) NOT NULL,
   col177 varchar(40) NOT NULL,
   col178 varchar(40) NOT NULL,
   col179 varchar(40) NOT NULL,
   col180 varchar(40) NOT NULL,
   col181 varchar(40) NOT NULL,
   col182 varchar(40) NOT NULL,
   col183 varchar(40) NOT NULL,
   col184 varchar(40) NOT NULL,
   col185 varchar(40) NOT NULL,
   col186 varchar(40) NOT NULL,
   col187 varchar(40) NOT NULL,
   col188 varchar(40) NOT NULL,
   col189 varchar(40) NOT NULL,
   col190 varchar(40) NOT NULL,
   col191 varchar(40) NOT NULL,
   col192 varchar(40) NOT NULL,
   col193 varchar(40) NOT NULL,
   col194 varchar(40) NOT NULL,
   col195 varchar(40) NOT NULL,
   col196 varchar(40) NOT NULL,
   col197 varchar(40) NOT NULL,
   col198 varchar(40) NOT NULL,
   PRIMARY KEY (col1)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

```

## Referenz:

  * https://mariadb.com/kb/en/innodb-limitations/

## Ausweg 

  * RocksDB (Sharding), TokuDB, ColumnStore, Partition
