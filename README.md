# MySQL Databse manipulation

```sql
use sakila;
```

-- 1

```sql
select first_name, last_name from actor;

select concat(first_name, " ", last_name) as `Actor Name` from actor;
```

-- 2

```sql
select actor_id, first_name, last_name from actor
where first_name = "Joe";

select actor_id, first_name, last_name from actor
where last_name like "%g%"
and last_name like "%e%"
and last_name like "%n%";

select actor_id, last_name, first_name from actor
where last_name like "%l%"
and last_name like "%i%";

select country_id, country from country
where country in ("Afghanistan","Bangladesh","China");
```

-- 3 

```sql
alter table actor
add middle_name varchar(50)
after first_name;

alter table actor
change middle_name middle_name blob;

alter table actor
drop middle_name;
```

-- 4

```sql
select last_name, count(*) from actor
group by last_name;

select last_name, count(*) from actor
group by last_name having count(*) > 1;

update actor 
set first_name = "Harpo"
where first_name = "Groucho"
and last_name = "Williams";

update actor
set first_name = 
(select if(first_name = "Harpo", "GROUCHO", "MUCHO GROUCHO"))
where actor_id = 172;
```

-- 5

```sql
show create table address;

-- 6

select s.first_name, s.last_name, a.address
from staff s join address a 
on s.address_id = a.address_id;

select s.first_name, s.last_name, p.amount
from staff s join (
	select staff_id, sum(amount) as amount 
	from payment
    where payment_date like "2005-08%"
	group by staff_id) p
on s.staff_id = p.staff_id;

select f.title, fa.actor_count
from film f inner join (
	select film_id, count(*) as actor_count 
    from film_actor 
    group by film_id
    ) fa
on f.film_id = fa.film_id;

select count(*) from inventory
where film_id = (
	select film_id from film 
    where title = "Hunchback Impossible");
    
select c.first_name, c.last_name, p.amount
from customer c join (
	select customer_id, sum(amount) as amount
    from payment
    group by customer_id
	) p
on c.customer_id = p.customer_id
order by last_name;
```

-- 7

```sql
select title from film
where language_id = (
	select language_id from language
    where name = "English")
and (title like "K%" or title like "Q%");

select first_name, last_name from actor
where actor_id in (
	select actor_id from film_actor
    where film_id = (
		select film_id from film
        where title = "Alone Trip"
	)
);

select first_name, last_name, email from customer
left join address 
on customer.address_id = address.address_id
left join city 
on address.city_id = city.city_id
inner join country
on city.country_id = country.country_id
where country = "Canada";

select title from film
join film_category fc 
on film.film_id = fc.film_id
join category c 
on fc.category_id = c.category_id
where c.name = "Family";


select title, n_rents from (
	select film_id, count(*) as n_rents 
    from rental r join inventory i
	on r.inventory_id = i.inventory_id
	group by film_id
) ri join film
on film.film_id = ri.film_id
order by n_rents desc;

# Since each store has exactly one staff member...
select staff_id as store_id, sum(amount) as amount
from payment 
group by staff_id;

select store_id, city, country
from store s
left join address a
on s.address_id = a.address_id
left join city c 
on a.city_id = c.city_id
left join country co
on c.country_id = co.country_id;

select * from category; #name, category_id
select * from film_category; #category_id, film_id
select * from inventory; #film_id, inventory_id
select * from rental; #inventory_id, rental_id
select * from payment; #rental_id, amount
```

-- 7h, 8

```sql
create view top_genres as
select c.name, sum(amount) as amount
from category c
left join film_category fc
on c.category_id = fc.category_id
left join inventory i 
on fc.film_id = i.film_id
left join rental r
on i.inventory_id = r.inventory_id
left join payment p
on r.rental_id = p.rental_id
group by c.name
order by amount desc
limit 5;
```
-- 8

```sql
select * from top_genres;

drop view top_genres;
```