# [SQL for Data Analysis : Beginner MySQL Business Intelligence](https://bgt1499.github.io/Barbara_Portfolio/)

# THE SITUATION

You and your business partner were recently approached by another local business owner
who is interested in purchasing Maven Movies. He primarily owns restaurants and bars, so he
has lots of questions for you about your business and the rental business in general. His offer
seems very generous, so you are going to entertain his questions.


# THE OBJECTIVE

Use MySQL to:

Leverage your SQL skills to extract and analyze data from various tables in the Maven
Movies database to answer your potential Acquirer’s questions. Each question will require
you to write a multi-table SQL query, joining at least two tables.


# Final_Project
1. My partner and I want to come by each of the stores in person and meet the managers. Please send over
the managers’ names at each store, with the full address of each property (street address, district, city, and
country please).

SELECT 
	staff.store_id,
    staff.first_name,
    staff.last_name,
    address.address,
    city.city,
    country.country
FROM staff
LEFT JOIN store
	ON staff.staff_id = store.manager_staff_id
LEFT JOIN address
	ON staff.address_id = address.address_id
LEFT JOIN city
	ON address.address_id = city.city_id
LEFT JOIN country
	ON city.country_id = country.country_id



2. I would like to get a better understanding of all of the inventory that would come along with the business.
Please pull together a list of each inventory item you have stocked, including the store_id number, the
inventory_id, the name of the film, the film’s rating, its rental rate and replacement cost.

SELECT 
   inventory.inventory_id,
    inventory.store_id,
    film.title,
    film.rating,
    film.rental_rate,
    film.replacement_cost
FROM inventory   
LEFT JOIN film
	ON   inventory.film_id  =  film.film_id 


3. From the same list of films you just pulled, please roll that data up and provide a summary level overview of
your inventory. We would like to know how many inventory items you have with each rating at each store.

SELECT 
	inventory.store_id,
    film.rating,
    count(inventory.inventory_id) AS total_inventory
FROM inventory
LEFT JOIN film
	ON inventory.film_id =  film.film_id
GROUP BY inventory.store_id,
		 film.rating


4. Similarly, we want to understand how diversified the inventory is in terms of replacement cost. We want to
see how big of a hit it would be if a certain category of film became unpopular at a certain store.
We would like to see the number of films, as well as the average replacement cost, and total replacement
cost, sliced by store and film category.

SELECT 
	inventory.store_id,
	category.name AS category,
	count(inventory.film_id) AS number_of_films,
    avg(replacement_cost) AS average_replacement_cost,
    sum(replacement_cost) AS total_replacement_cost
FROM inventory
	LEFT JOIN film
		ON inventory.film_id = film.film_id
	LEFT JOIN film_category
		ON film.film_id = film_category.film_id
	LEFT JOIN category
		ON film_category.category_id = category.category_id
GROUP BY inventory.store_id,
		 category.name
ORDER BY total_replacement_cost DESC


5. We want to make sure you folks have a good handle on who your customers are. Please provide a list
of all customer names, which store they go to, whether or not they are currently active, and their full
addresses – street address, city, and country.

SELECT 
	customer.first_name,
    customer.last_name,
    customer.store_id,
    CASE WHEN  active = 1 THEN 'activo' ELSE 'inactivo' END AS cliente_status,
	address.address,
	city.city,
    country.country
FROM customer
	LEFT JOIN address
		ON customer.address_id = address.address_id
	LEFT JOIN city
		ON address.city_id = city.city_id
	LEFT JOIN country
		ON city.country_id = country.country_id


6. We would like to understand how much your customers are spending with you, and also to know who your
most valuable customers are. Please pull together a list of customer names, their total lifetime rentals, and the
sum of all payments you have collected from them. It would be great to see this ordered on total lifetime value,
with the most valuable customers at the top of the list.

SELECT
	customer.first_name,
    customer.last_name,
    count(payment.rental_id) AS total_lifetime_rentals,
    SUM(payment.amount) AS all_payments
FROM customer
	LEFT JOIN payment
		ON customer.customer_id = payment.customer_id
GROUP BY
	customer.first_name,
    customer.last_name
ORDER BY 
	all_payments DESC


7. My partner and I would like to get to know your board of advisors and any current investors. Could you
please provide a list of advisor and investor names in one table? Could you please note whether they are an
investor or an advisor, and for the investors, it would be good to include which company they work with.

SELECT
	'advisor' AS type,
    first_name,
    last_name,
    NULL
FROM advisor

UNION

SELECT
	'investor' AS type,
    first_name,
    last_name,
    company_name AS company
FROM investor;


8. We're interested in how well you have covered the most-awarded actors. Of all the actors with three types of
awards, for what % of them do we carry a film? And how about for actors with two types of awards? Same
questions. Finally, how about actors with just one award?

SELECT 
       CASE 
		WHEN awards = 'Emmy, Oscar, Tony ' THEN '3 awards'
        WHEN awards IN('Emmy, Oscar', 'Oscar, Tony', 'Emmy,Tony') THEN '2 awards'
        ELSE '1 award'
	END AS number_of_awards,
    AVG(CASE WHEN actor_id IS NULL THEN 0 ELSE 1 END) AS pct_w_one_film
FROM actor_award
GROUP BY number_of_awards
ORDER BY number_of_awards

