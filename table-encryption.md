# Table Encryption 

## Step 1: Set up keys 
```
mkdir -p /etc/mysql/encryption;
echo "1;"$(openssl rand -hex 32) > /etc/mysql/encryption/keyfile;

openssl rand -hex 128 > /etc/mysql/encryption/keyfile.key;
openssl enc -aes-256-cbc -md sha1 -pass file:/etc/mysql/encryption/keyfile.key -in /etc/mysql/encryption/keyfile -out /etc/mysql/encryption/keyfile.enc;

rm -f /etc/mysql/encryption/keyfile;

chown -R mysql:mysql /etc/mysql;
chmod -R 500 /etc/mysql;


```

## Step 2: Verify data before encryption 

```
cd /var/lib/mysql/mysql
strings gtid_slave_pos.ibd 

```

## Step 3: Setup configuration 

````
# debian / ubuntu 
nano /etc/mysql/mariadb.conf.d/z_encryption.cnf
```

```
# centos / rocky /redhat 
nano /etc/my.cnf.d/z_encryption.cnf 
```

```
[mysqld]
plugin_load_add = file_key_management
file_key_management_filename = /etc/mysql/encryption/keyfile.enc
file_key_management_filekey = FILE:/etc/mysql/encryption/keyfile.key
file_key_management_encryption_algorithm = AES_CTR

innodb_encrypt_tables = FORCE
innodb_encrypt_log = ON
innodb_encrypt_temporary_tables = ON

encrypt_tmp_disk_tables = ON
encrypt_tmp_files = ON
encrypt_binlog = ON
aria_encrypt_tables = ON

innodb_encryption_threads = 4
innodb_encryption_rotation_iops = 2000


```

## Step 4: Restart server 

```
systemctl restart mariadb 
```

## Step 5: Verify encryption

```
cd /var/lib/mysql/mysql
strings gtid_slave_pos;
mysql;
```

```
use information_schema;
select * from innodb_tablespaces_encryption;
SELECT CASE WHEN INSTR(NAME, '/') = 0 
                   THEN '01-SYSTEM TABLESPACES'
                   ELSE CONCAT('02-', SUBSTR(NAME, 1, INSTR(NAME, '/')-1)) END 
                     AS "Schema Name",
         SUM(CASE WHEN ENCRYPTION_SCHEME > 0 THEN 1 ELSE 0 END) "Tables Encrypted",
         SUM(CASE WHEN ENCRYPTION_SCHEME = 0 THEN 1 ELSE 0 END) "Tables Not Encrypted"
FROM information_schema.INNODB_TABLESPACES_ENCRYPTION
GROUP BY CASE WHEN INSTR(NAME, '/') = 0 
                   THEN '01-SYSTEM TABLESPACES'
                   ELSE CONCAT('02-', SUBSTR(NAME, 1, INSTR(NAME, '/')-1)) END
ORDER BY 1;
```

## Step 6: disable encryption runtime 

```
SET GLOBAL innodb_encrypt_tables = OFF;
```

```
# Create a user that is not allowed to do so .... no set global 
create user noroot@'localhost' identified by 'password';
grant all on *.* to noroot@'localhost';
revoke super on *.* from noroot@'localhost';
```

## working with mysqlbinlog and encryption 

```
mysqlbinlog -vv --read-from-remote-server --socket /run/mysqld/mysqld.sock mysqld-bin.000003 | less
```


## Ref:

  * https://mariadb.com/de/resources/blog/mariadb-encryption-tde-using-mariadbs-file-key-management-encryption-plugin/
