# Домашнее задание к занятию "`SQL ч.2`" - `Никифоров Роман`

[Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1 

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

    фамилия и имя сотрудника из этого магазина;
    город нахождения магазина;
    количество пользователей, закреплённых в этом магазине.


```
SELECT CONCAT(s2.first_name, ' ', s2.last_name) AS 'Name staff', c2.city AS City, COUNT(customer_id) AS 'Total customer'
FROM customer c
JOIN store s ON s.store_id =c.store_id
JOIN staff s2 ON s2.store_id = s.store_id 
JOIN address a ON a.address_id = s2.address_id
JOIN city c2 ON c2.city_id  = a.city_id
GROUP BY s.store_id, c2.city_id, s2.first_name, s2.last_name
HAVING COUNT(customer_id) > 300;

```

---

### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

```

SELECT COUNT(film_id) AS 'Total Films'
FROM film
WHERE `length` > (SELECT AVG(`length`)  FROM film);

```

---

### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

```

SELECT DATE_FORMAT(payment_date, "%M %Y"), COUNT(rental_id), SUM(amount)
FROM payment p
GROUP BY DATE_FORMAT(payment_date, "%M %Y")
ORDER BY SUM(amount) DESC LIMIT 1;

```

---
