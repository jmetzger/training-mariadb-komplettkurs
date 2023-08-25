# ssl - mariadb - Centos/Redhat/Rocky - Distribution Version

## Variant 1: Setup 1-way ssl encryption 

### Create CA and Server-Key 

```

# On Server - create ca and certificates 
sudo mkdir -p /etc/my.cnf.d/ssl
sudo cd /etc/my.cnf.d/ssl

# create ca.  
sudo openssl genrsa 4096 > ca-key.pem

# create ca-certificate 
# Common Name: MariaDB CA 
sudo openssl req -new -x509 -nodes -days 365000 -key ca-key.pem -out ca-cert.pem

# create server-cert 
# Common Name: server1.training.local 
# Password: --- leave empty ----
sudo openssl req -newkey rsa:2048 -days 365000 -nodes -keyout server-key.pem -out server-req.pem

# Next process the rsa - key 
sudo openssl rsa -in server-key.pem -out server-key.pem

# Now sign the key 
sudo openssl x509 -req -in server-req.pem -days 365000 -CA ca-cert.pem -CAkey ca-key.pem -set_serial 01 -out server-cert.pem

```

### Verify certificates 

```
openssl verify -CAfile ca-cert.pem server-cert.pem 


```

### Configure Server 
```
# create file 
# /etc/my.cnf.d/z_ssl.cnf 
[mysqld]
ssl-ca=/etc/my.cnf.d/ssl/ca-cert.pem
ssl-cert=/etc/my.cnf.d/ssl/server-cert.pem
ssl-key=/etc/my.cnf.d/ssl/server-key.pem
## Set up TLS version here. For example TLS version 1.2 and 1.3 ##
# Starts from mariadb 10.4.6 not possible before. !!!! 
tls_version = TLSv1.2,TLSv1.3

# Set ownership 
chown -vR mysql:mysql /etc/my.cnf.d/ssl/

```

### Restart and check for errors 
```
systemctl restart mariadb
journalctl -u mariadb 

```

### Test connection on client 

```
# only if we use option --ssl we will connect with ssl 
mysql --ssl -uxyz -p -h <ip-of-server>
mysql>status
SSL:                    Cipher in use is TLS_AES_256_GCM_SHA384

```

### Force to use ssl 


```
# on server 
# now client can only connect, when using ssl 
mysql> grant USAGE on *.* to remote@10.10.9.144 require ssl;
```





## Variant 2: 1-way ssl-encryption but checking server certificate 

### Prerequisites 

```
server1: 192.168.56.103 
client1: 192.168.56.104
```

### Copy ca-cert to client 

```
# on server1 
cd /etc/my.cnf.d/ssl
scp ca-cert.pem kurs@192.168.56.104:/tmp 

# on clien1 
cd /etc/my.cnf.d 
mkdir ssl 
cd ssl
mv /tmp/ca-cert.pem . 
```

### Configure client1 - client -config  

```
sudo vi /etc/my.cnf.d/mysql-clients.cnf

Append/edit in [mysql] section:

## MySQL Client Configuration ##
ssl-ca=/etc/my.cnf.d/ssl/ca-cert.pem

##  Force TLS version for client too
#tls_version = TLSv1.2,TLSv1.3
### This option is disabled by default ###
### ssl-verify-server-cert ###

# only works if you have no self-signed certificate
ssl-verify-server-cert
ssl

# domain-name in hosts setzen 
# because in dns
vi /etc/hosts 
192.168.56.103 server1.training.local 

# now you to connect with hostname
# otherwice no check against certificate can be done 
mysql -uext -p -h server1.training.local 

# if it does not work, you get 
ERROR 2026 (HY000): SSL connection error: Validation of SSL server certificate failed

```

## Variant 3: 2-way - Security (Encryption) - validated on server and client 

### Client - Create certificate on server
  * we are using the same ca as on the server

```
# on server1
cd /etc/my.cnf.d/ssl
# Bitte Common-Name: MariaDB Client 
openssl req -newkey rsa:2048 -days 365 -nodes -keyout client-key.pem -out client-req.pem

# process RSA - Key 
# Eventually also works without - what does it do ? 
# openssl rsa -in client-key.pem -out client-key.pem

# sign certficate with CA 
openssl x509 -req -in client-req.pem -days 365 -CA ca-cert.pem -CAkey ca-key.pem -set_serial 01 -out client-cert.pem

```

### Client - Zertifikate validieren 

```
openssl verify -CAfile ca-cert.pem client-cert.pem
```

### Zertifikate für Client zusammenpacken

```
mkdir cl-certs; cp -a client* cl-certs; cp -a ca-cert.pem cl-certs ; tar cvfz cl-certs.tar.gz cl-certs 
```


### Zertifikate auf Client transferieren 

```
scp cl-certs.tar.gz kurs@192.168.56.104:/tmp 
```



### Zertifikate einrichten 

```
# on client1 
# cleanup old config 
rm /etc/my.cnf.d/ssl/ca-cert.pem 

mv /tmp/cl-certs.tar.gz /etc/my.cnf.d/ssl
cd /etc/my.cnf.d; tar xzvf cl-certs.tar.gz 

vi mysql-clients.cnf 
[mysql]
ssl-ca=/etc/my.cnf.d/cl-certs/ca-cert.pem
ssl-cert=/etc/my.cnf.d/cl-certs/client-cert.pem
ssl-key=/etc/my.cnf.d/cl-certs/client-key.pem

```

### Test the certificate

```
# on server1 verify: X509 for user 
select user,ssl_type from mysql.user where user='ext'

# connect from client1 
# Sollte die Verbindung nicht klappen stimmt auf dem 
# Client etwas mit der Einrichtung nicht
mysql -uext -p -h192.168.56.103
mysql> status 

```


## Ref 

  * https://mariadb.com/kb/en/securing-connections-for-client-and-server/
  * https://www.cyberciti.biz/faq/how-to-setup-mariadb-ssl-and-secure-connections-from-clients/
  

