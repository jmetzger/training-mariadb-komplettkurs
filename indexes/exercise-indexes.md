# Exercise Indexes 

## Exercise I 

```
mysql
```

```
use sakila;
create table actorneu as select * from actor;

-- Test 1
explain select * from actorneu where actor_id = 1;
explain select * from actor where actor_id = 1; 

-- Test 2
explain select * from actorneu where actor_id > 10 and actor_id < 1000;
explain select * from actor where actor_id > 10 and actor_id < 1000;

-- Test 3 
describe actor;
explain select last_name from actor where actor_id > 2;
explain select * from actor where last_name like 'A%';
explain select * from actor where last_name like '%N';

```

## Exercise II 

```
create index idx_actorneu_first_name_last_name on actorneu (first_name,last_name);
show index from actorneu;



```
