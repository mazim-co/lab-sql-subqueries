-- 3.05 LAB SQL Subqueries
-- How many copies of the film Hunchback Impossible exist IN the inventory system?

SELECT count(inventory_id) AS copys, film_id FROM sakila.inventory
WHERE (
SELECT film_id FROM sakila.film
WHERE title = 'Hunchback Impossible') = film_id
GROUP BY film_id;


-- LIST ALL films whose length IS longer THAN the average of ALL the films.

SELECT title, length FROM sakila.film
WHERE length > ( 
SELECT AVG(length) FROM sakila.film);




-- USE subqueries TO display ALL actors who appear IN the film Alone Trip.

SELECT * FROM sakila.film_actor
WHERE (
SELECT film_id FROM sakila.film
WHERE title = 'Alone Trip') = film_id;





-- Sales have been lagging among young families, AND you wish TO target ALL family movies FOR a promotion. Identify ALL movies categorized AS family films.


SELECT c.category_id, c.name AS category_name, title FROM sakila.film f
JOIN sakila.film_category fc ON fc.film_id = f.film_id
JOIN sakila.category c ON c.category_id = fc.category_id
WHERE (
	SELECT category_id FROM sakila.category 
	WHERE NAME = 'Family'
			) = fc.category_id;



-- Get NAME AND email FROM customers FROM Canada USING subqueries. DO the same WITH joins. Note that TO CREATE a JOIN, you will have TO identify the correct TABLES WITH their PRIMARY KEYS AND FOREIGN KEYS, that will HELP you get the relevant information.



SELECT country, first_name, last_name, email FROM sakila.customer c
JOIN sakila.address a ON a.address_id = c.address_id
JOIN sakila.city ci ON ci.city_id = a.city_id
JOIN sakila.country co ON co.country_id = ci.country_id
WHERE (
SELECT country_id FROM sakila.country WHERE country = 'Canada') = ci.country_id;




-- Which are films starred BY the most prolific actor? Most prolific actor IS defined AS the actor that has acted IN the most number of films. FIRST you will have TO find the most prolific actor AND THEN USE that actor_id TO find the different films that he/she starred.

SELECT actor_id, film_id FROM sakila.film_actor fa
WHERE actor_id =
(SELECT actor_id FROM
(
SELECT count(*), actor_id FROM sakila.film_actor
GROUP BY actor_id
ORDER BY count(*) DESC
LIMIT 1)sub);




-- Films rented BY most profitable customer. You can USE the customer TABLE AND payment TABLE TO find the most profitable customer ie the customer that has made the largest sum of payments


SELECT customer_id
FROM sakila.customer
INNER JOIN payment USING (customer_id)
GROUP BY customer_id
ORDER BY sum(amount) DESC
LIMIT 1;

SELECT film_id, title, rental_date, amount
FROM sakila.film
INNER JOIN inventory USING (film_id)
INNER JOIN rental USING (inventory_id)
INNER JOIN payment USING (rental_id)
WHERE rental.customer_id = (
SELECT customer_id
FROM customer
INNER JOIN payment
USING (customer_id)
GROUP BY customer_id
ORDER BY sum(amount) DESC
LIMIT 1
)
ORDER BY rental_date DESC;

-- Customers who spent more THAN the average payments.

SELECT customer_id, sum(amount) AS payment
FROM sakila.customer
INNER JOIN payment USING (customer_id)
GROUP BY customer_id
HAVING sum(amount) > (
SELECT AVG(total_payment)
FROM (
SELECT customer_id, sum(amount) total_payment
FROM payment
GROUP BY customer_id
) t
)
ORDER BY payment DESC;



