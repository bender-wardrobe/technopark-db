1. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT rental_id FROM rental
WHERE rental_date BETWEEN '2005-07-01' AND '2005-08-01'
  AND customer_id = 2
ORDER BY rental_date, inventory_id;
```
2. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT rental_id FROM rental
WHERE EXTRACT(SECOND FROM '2005-07-01'::timestamp - rental_date) > 0
  AND customer_id = 2
ORDER BY rental_date;
```

3. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT rental_id FROM rental
WHERE rental_date BETWEEN '2005-07-01' AND '2005-08-01'
  AND customer_id IN (2, 42, 73);
```

4. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT city.city_id, city.city, country.country
FROM city
JOIN country ON city.country_id = country.country_id
WHERE LOWER(LEFT(city.city, 2)) = 'mo';
```
5. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT COUNT(*), country.country
FROM city
JOIN country ON city.country_id = country.country_id
GROUP BY country.country;
```
6. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT film.title, SUM(payment.amount)
FROM customer
JOIN rental ON (rental.customer_id = customer.customer_id)
JOIN inventory ON (rental.inventory_id = inventory.inventory_id)
JOIN film ON (inventory.film_id = film.film_id)
JOIN payment ON (payment.rental_id = rental.rental_id)
WHERE EXTRACT(YEAR FROM rental.rental_date) = 2005
  AND customer.store_id = 2
GROUP BY film.title;
```

7. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT country.country, COUNT(*)
FROM store
JOIN address ON (store.address_id = address.address_id)
JOIN city ON (city.city_id = address.city_id)
JOIN country ON (country.country_id = city.country_id)
WHERE LOWER(LEFT(country.country, 1)) = 'a'
GROUP BY country.country;
```

8. Оптимально ли составлен запрос? Какие индексы нужно построить для эффективного выполнения запроса и почему?
```sql
SELECT film_id, title, description
FROM film
WHERE (
  SELECT COUNT(*)
  FROM actor
  JOIN film_actor ON (film_actor.actor_id = actor.actor_id)
  WHERE film_actor.film_id = film.film_id
    AND LOWER(actor.last_name) = 'monroe'
) > 0
  AND film.rating = 'G'
  AND film.release_year BETWEEN 2005 AND 2008;
```