# State Changes 

## How it works ?

 1.  On one node in the cluster, a state change occurs on the database.
 2.  In the database, the wsrep hooks translate the changes to the write-set.
 3.  dlopen() makes the wsrep provider functions available to the wsrep hooks.
 4.  The Galera Replication plugin handles write-set certification and replication to the cluster

## SST / IST

### State Snaphost Transfer (SST)

  *  Full Transfer is done
  *  Methods: mariabackup, mysqldump, rsync
  *  Some are blocking (mysqldump, rsync), some are not (mariabackup) 
     * Means: it can not be written to the donor node in the meantime 
  *  Please do not use !!! xtrabackup or xtrabackup_v2 for mariadb because of encryption 

### Incremental State Transfer (IST)

*  A node only receives the missing write sets.
*  Is only done, when
    * The missing write-sets are in the gcache of the donor
    * Otherwice SST is done
