use sakila;

SELECT * FROM sakila.actor;


-- 1a

-- Display first and last names of all actors from the table actor:

SELECT first_name, last_name FROM actor;


-- 1b
-- Display the first and last name of each actor in a single column in upper case letters. Name the column Actor Name:

SELECT upper(concat(first_name, ' ' , last_name)) AS Actor_Name FROM actor;


-- 2a
-- Find the ID number, first name, last name of an actor named "Joe":

SELECT actor_id, first_name, last_name FROM actor WHERE first_name =  "Joe";


-- 2b
-- Find all actors whose last name contain the letters GEN:

SELECT last_name FROM actor WHERE last_name like '%GEN%';


-- 2c
-- Find all actors  whose last names contain the letters LI. Order the rows by last name and first name:

SELECT last_name, first_name FROM actor WHERE last_name like '%LI%' order by last_name AND first_name;

-- 2d
-- Display the country_id and country columns of Afganistan, Bangladesh and China:
SELECT country_id, country FROM country WHERE country IN ('Afghanistan', 'Bangladesh', 'China');


-- 3a
-- Add column 'description' in the  actor table:

ALTER TABLE actor ADD COLUMN description BLOB NOT NULL;


-- 3b
-- Delete the column  'description' from the actor table:

ALTER TABLE actor DROP COLUMN description;


-- 4a
-- List last names with count:

SELECT last_name,  count(last_name) FROM actor group by(last_name); 


-- 4b
-- List actors with shared last name:

SELECT last_name,  count(last_name) FROM actor group by(last_name) HAVING count(last_name) > 1; 

-- 4c
-- Write query to update the name   GROUCHO WILLIAMS to HARPO WILLIAMS:

UPDATE actor
SET first_name = 'GROUCHO'  
WHERE first_name = 'HARPO' AND last_name = 'WILLIAMS';

-- 4d

UPDATE actor

SET first_name = 'HARPO' 

WHERE first_name = 'GROUCHO'  AND last_name = 'WILLIAMS' ;

-- 5a

SHOW CREATE TABLE address;

-- 6a
-- Display first_name, last_name and address of each staff member using table staff and address:

SELECT staff.first_name, staff.last_name, address.address_id
FROM staff
JOIN address
ON staff.staff_id=address.address_id;

-- 6b
-- Display the total amount for the month August using Join, staff and payment table
select sum(amount), staff.staff_id, payment_date
from staff                  
join payment
on payment.staff_id=staff.staff_id
where payment_date like '2005-08%';


-- 6c
-- list film and actors

select title, actor_id
from film
inner join film_actor
on film.film_id=film_actor.film_id;

-- 6d

select count(inventory_id)
from inventory

where film_id
in (
	select film_id
    from film
	where title = 'Hunchback Impossible'
    );

-- 6e

select first_name, last_name, total_paid from customer join 
(select customer.customer_id as cust_id, sum(payment.amount) as total_paid from customer
join payment on customer.customer_id=payment.customer_id
group by customer.customer_id) as cust_paid on customer.customer_id=cust_paid.cust_id
order by last_name;


-- 7a

select title, language_id 
	from film
where (title like 'k%' or title like 'Q%') 
and language_id in (
	SELECT language_id 
    FROM language 
    where name = 'english');

-- 7b

SELECT first_name, last_name
FROM film_actor 
JOIN film ON film_actor.film_id=film.film_id
WHERE  film.title
IN (	
	  SELECT film.title
	   FROM film
	   WHERE film.title = 'Alone Trip'
    );


-- 7c 

select  customer.first_name, customer.last_name, customer.email 
from customer
join country on country_id
where country = 'Canada';


-- 7d
select category_id 
from film_category 
where film_id
in (
	select film_id 
	from film
	where category_id = 'Family');
    
-- 7e
select title, rental_duration from film
order by rental_duration desc;


-- 7f
select store_id, amount from payment
join store on store_id=staff_id
where rental_id in 
(
	select rental_id from rental
);


-- 7g

select store_id 
from store
where city in
(	
    select city from city
	where country_id in
		( 
			select country 
        from country
	)
        );

-- 7h
select amount from payment
where special_features in 
(
	select special_features from film
    where rental_id in (
		select rental_id from rental
        limit 5
)
	);
    
-- 8a
    
 create view gross_revenue as 
 select amount from payment
 join film on category.film_id=film.film_id
 limit 5;
 
-- 8b
show create view gross_revenue;


-- 8c

drop view gross_revenue;
