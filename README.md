# SQL_Sakila_Database
This repository contains the Sakila dataset, a sample database originally created by MySQL for demonstration purposes. The Sakila database is a standard schema that can be used for testing, benchmarking, or learning about relational databases. 


# Provide the payment amount from payment table where the payment amount is greater than 2

SELECT amount
FROM payment
WHERE amount > 2;

#Provide film titles and the replacement_cost where the rating is PG and the replacement cost is less than 10

SELECT title, replacement_cost
FROM film
WHERE rating = 'PG' AND replacement_cost < 10;

# Calculate the average rental price (rental_rate) for the movies in each rating, provide the answer with only 1 decimal place. Use table film. 
SELECT rating, ROUND(AVG(rental_rate), 1) AS average_rental_price
FROM film
GROUP BY rating;

# Print the names of all the customers (first_name) and count the length of each name (how many letters there are in the name) next to the names column. Use the customer table. 
SELECT first_name, LENGTH(first_name) AS name_length
FROM customer;

#Use `JOIN` to display the first and last names, as well as the address, of each staff member. Use the tables `staff` and ‘address'
SELECT staff.first_name, staff.last_name, address.address
FROM staff
JOIN address ON staff.address_id = address.address_id;

#Use `JOIN` to display the total amount rung up by each staff member in August of 2005. Use tables `staff` and `payment`.
SELECT staff.staff_id, staff.first_name, staff.last_name, SUM(payment.amount) AS total_amount
FROM staff
JOIN payment ON staff.staff_id = payment.staff_id
WHERE payment.payment_date >= date'2005-08-01'
AND payment_date <= date'2005-08-31'
GROUP BY staff.staff_id;
# Same SQL question in a different way of solving
SELECT staff.staff_id, staff.first_name, staff.last_name, SUM(payment.amount) AS total_amount
FROM staff
JOIN payment ON staff.staff_id = payment.staff_id
WHERE month(payment_date) = 08
AND year(payment_date) = 2005
GROUP BY staff.staff_id;

#List each film and the number of actors who are listed for that film. Use tables `film_actor` and `film`. Use inner join.
SELECT film.title, COUNT(film_actor.actor_id) AS actor_count
FROM film
JOIN film_actor ON film.film_id = film_actor.film_id
GROUP BY film.title;

# How many copies of the film `Hunchback Impossible` exist in the inventory system?
SELECT title, COUNT(inventory.inventory_id) AS film_count
FROM film
JOIN inventory ON film.film_id = inventory.film_id
WHERE film.title = 'Hunchback Impossible'
GROUP BY 1;

# Using the tables ‘payment’ and ‘customer’ and the JOIN command, list the total paid by each customer. List the customers alphabetically by last name.
SELECT count(distinct customer_id) AS customers 
FROM
(SELECT customer.first_name, customer.last_name, customer.customer_id, SUM(payment.amount) AS total_paid
FROM customer
JOIN payment ON customer.customer_id = payment.customer_id
GROUP BY 1,2,3
ORDER BY customer.last_name
) A
WHERE total_paid < 90;
# with WHEN clus 
SELECT case when total_paid < 50 then 'less than 50'
            when total_paid >= 50 and total_paid < 100 then 'between 50 and 100'
            when total_paid >= 100 and total_paid < 150 then 'between 100 and 150'
            when total_paid >= 150 then 'more than 150' 
            END as catagory,
	  count(distinct customer_id) AS customers 
FROM
(SELECT customer.first_name, customer.last_name, customer.customer_id, SUM(payment.amount) AS total_paid
FROM customer
JOIN payment ON customer.customer_id = payment.customer_id
GROUP BY 1,2,3
ORDER BY customer.last_name
) A
GROUP BY 1;

# You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

SELECT 
      c.first_name, 
      c.last_name, 
      c.email, 
      co.country
FROM customer c
JOIN address a ON c.address_id = a.address_id
JOIN city ci ON a.city_id = ci.city_id
JOIN country co ON ci.country_id = co.country_id

WHERE
     country = 'Canada';

# Write a query to display how much business, in dollars, each store brought in. 
SELECT address, SUM(amount) as 'total business' 
from payment p 
JOIN(		
	SELECT address, rental_id 
    FROM rental r 
    JOIN( 
		SELECT address, inventory_id 
        FROM inventory i
			JOIN (
				SELECT s.store_id as store_id, a.address 
                FROM store s 
				JOIN address a ON a.address_id = s.address_id) b
				ON i.store_id = b.store_id
				) c ON c.inventory_id = r.inventory_id)
                d ON d.rental_id = p.rental_id 
GROUP BY address;

# Provide the names (first_name) and surnames (last_name) of the customers who rented the movie "AGENT TRUMAN". Use tables for customer, rental, inventory, film.
SELECT 
    customer.first_name, 
    customer.last_name 
FROM 
    customer
    JOIN rental ON customer.customer_id = rental.customer_id
    JOIN inventory ON rental.inventory_id = inventory.inventory_id
    JOIN film ON inventory.film_id = film.film_id
WHERE 
    film.title = 'AGENT TRUMAN';

# Add up the rental duration (rental_duration) for each movie category (use the category name, not just the category id). Print only those categories with a rental_duration greater than 300. Use the tables film, film_category, category. 
SELECT 
    category.name, 
    SUM(film.rental_duration) AS total_duration
FROM 
    film
    JOIN film_category ON film.film_id = film_category.film_id
    JOIN category ON film_category.category_id = category.category_id
GROUP BY 
    category.name
HAVING 
    total_duration > 300;
    
    
