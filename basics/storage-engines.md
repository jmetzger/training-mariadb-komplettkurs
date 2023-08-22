# Storage Engines 

## Why ?

```
Let's you choose:
How your data is stored
```

## What ?

  * Performance, features and other characteristics you want

## Where ? 

  * Theoretically you can use a different engine for every table 
  * But: For performance optimization and future, it is better to concentrate on one 

## What do they do ?

  * In charge for: Responsible for storing and retrieving all data stored in MariaDB
  * Each storage engine has its:
    * Drawbacks and benefits
  * Server communicates with them through the storage engine API 
    * this interface hides differences
    * makes them largely transparent at query layer
    * api contains a couple of dozen low-level functions e.g. “begin a transaction”, “fetch the row that has this primary key”

## Storage Engine do not ....

  * Storage Engines do not parse SQL
  * Storage Engines do not communicate with each other

## They simply .....

  * They simply respond to requests from the server

## Which are the most important one ?

  * InnoDB (currently default engine) 
  * MyISAM/Aria
  * Memory
  * CSV
  * Blackhole (/dev/null)
  * Archive
  * Partition
  * (Federated/FederatedX)
