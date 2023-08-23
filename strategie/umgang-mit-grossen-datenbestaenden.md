# Umgang von mariadb mit grossen Daten 

## Mariabackup vs. mysqldump 

  * Bei großen Daten ist mariabackup zu empfehlen, da das rücksichern wesentlich schneller ist

## Änderung von Struktur 

  * Änderung können sehr teuer sein. (Originaltabelle wird gesperrt)
  * Alternative Lösung: pt-online-schema-change
    * https://docs.percona.com/percona-toolkit/pt-online-schema-change.html 
