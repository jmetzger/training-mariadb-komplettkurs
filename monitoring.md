# Monitoring 

## What to monitor ? 

  * Single nodes 
  * wsrep_local_state_comment -> synced # should be synced (but problem if it will not be on synced for a longer time)
  * wsrep_cluster_status # Primary // Non-Primary is BAD !! 
  * Does the node run
  * Necessary ports available 

## set threads in galera according to cert_deps 

```
Sequentially in Parallel

Last, you might monitor wsrep_cert_deps_distance. It will tell you the average distance between the lowest and highest sequence number, values a node can potentially apply in parallel.

Basically, this is the optimal value to set wsrep_slave_threads or wsrep_applier_threads, since it’s pointless to assign more slave threads than the number of transactions that can be applied in parallel.
```

## What to monitor 

  * https://galeracluster.com/library/training/tutorials/galera-monitoring.html


## pmmdemo (Percona Management und Monitoring Tool) 


  * https://pmmdemo.percona.com/graph/d/pmm-home/home-dashboard?orgId=1&refresh=1m&var-interval=$__auto_interval_interval&var-environment=All&var-node_name=pxc-80-2&var-service_name=All&var-cluster=All&var-replication_set=All&var-database=All&var-node_type=All&var-service_type=All&var-username=All&var-schema=All
  * https://www.percona.com/doc/percona-monitoring-and-management/deploy/server/docker.html
