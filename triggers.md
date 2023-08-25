# Triggers - Example  

## Step 1: Create the structure 

```
use sakila;
create table countries (
    country_id int auto_increment,
    name varchar(50) not null,
    primary key(country_id) 
);
```

```
INSERT INTO countries (name) values ('Germany'), ('Austria'); 
```

```
create table country_stats(
    country_id int,
    year int,
    population int,
    primary key (country_id, year),
    foreign key(country_id)
	references countries(country_id)
);
```

```
INSERT INTO country_stats (country_id, year, population) values (1,2020,100000);
```

```
create table population_logs(
    log_id int auto_increment,
    country_id int not null,
    year int not null,
    old_population int not null,
    new_population int not null,
    updated_at timestamp default current_timestamp,
    primary key(log_id)
);

```

## Create the trigger (Optional)

```
create trigger before_country_stats_update 
    before update on country_stats
    for each row
    insert into population_logs(
        country_id, 
        year, 
        old_population, 
        new_population
    )
    values(
        old.country_id,
        old.year,
        old.population,
        new.population
    );

```

## Step 2: Create trigger (the same) but with BEGIN/END - Block 

```
delimiter //
create trigger before_country_stats_update 
    before update on country_stats
    for each row

    BEGIN
    SET @anfang = 1;
    insert into population_logs(
        country_id, 
        year, 
        old_population, 
        new_population
    )
    values(
        old.country_id,
        old.year,
        old.population,
        new.population
    );
    END //
  
 delimiter ; 
    

```

## Step 3: Run a test 

```
update 
    country_stats
set 
    population = 1352617399
where 
    country_id = 1 and 
    year = 2020;

-- what's the new result 

select * from population_logs;

```

## Continue although we have an error (Optional)

```

delimiter //
create or replace trigger before_country_stats_update 
    before update on country_stats
    for each row

    BEGIN
    DECLARE CONTINUE HANDLER FOR 1146 
      SET @a= 1;
     


    SET @anfang = 1;
    insert into population_logs2(
        country_id, 
        year, 
        old_population, 
        new_population
    )
    values(
        old.country_id,
        old.year,
        old.population,
        new.population
    );
    END//

delimiter ;


```

```
update country_stats set population = 1352617399 where country_id = 1 and year = 2020;

```

## Ref:

  * https://mariadb.com/kb/en/trigger-overview/




## Ref with walkthrough 

  * https://mariadb.com/kb/en/trigger-overview/
