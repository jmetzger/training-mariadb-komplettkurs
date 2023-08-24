# Exercise Indexes 

```
create table actorneu as select * from actor;

-- Test 1
explain select * from actorneu where actor_id = 1;
explain select * from actor where actor_id = 1 

-- Test 2
explain select * from actorneu where actor_id > 10 and actor_id < 1000

```
