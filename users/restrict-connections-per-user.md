# Restrict Connections per User 


```
ALTER USER ext@'192.168.56.%' WITH MAX_USER_CONNECTIONS 10
```

## Reference 

  * https://mariadb.com/docs/server/reference/sql-statements/account-management-sql-statements/alter-user
