# Where does the Server Listen 

## How to check ? 

```
lsof -i | grep mariadb
# localhost means it does NOT listen to the outside now 
# mariadbd  5208           mysql   19u  IPv4  56942      0t0  TCP localhost:mysql (LISTEN)

netstat -tupel 
# or 
netstat -an 

```

## How to fix (Ubuntu -> Mariadb Foundation) 

```
nano /etc/mysql/mariadb.d/server.cnf
```

```
# In Section [mysqld] 
# Change bind-address to -> bind-address = 0.0.0.0
[mysqld]
bind-address = 0.0.0.0
```

```
systemctl restart mariadb
systemctl status mariadb
```
