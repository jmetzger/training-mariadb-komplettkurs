# Exercise Hitliste von slow_query_log erstellen 

```
dnf install -y https://repo.percona.com/yum/percona-release-latest.noarch.rpm && dnf install -y percona-toolkit
cd /var/lib/mysql
pt-query-digest server1-slow.log > /usr/src/report.txt
```
