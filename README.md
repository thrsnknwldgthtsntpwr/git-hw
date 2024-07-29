# Домашнее задание к занятию "`Работа с данными (DDL/DML)`" - `Никифоров Роман`

[Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1 

Получите уникальные названия районов из таблицы с адресами, которые начинаются на “K” и заканчиваются на “a” и не содержат пробелов.

```
SELECT DISTINCT district FROM address WHERE district LIKE 'K%' AND district LIKE '%a' AND district NOT LIKE "% %";

```

---

### Задание 2

Получите из таблицы платежей за прокат фильмов информацию по платежам, которые выполнялись в промежуток с 15 июня 2005 года по 18 июня 2005 года включительно и стоимость которых превышает 10.00.

```

SELECT * FROM payment WHERE DATE(payment_date) BETWEEN '2005-06-15' AND '2005-06-18' AND amount > 10.00;


```

---

### Задание 3

Получите последние пять аренд фильмов.

```

SELECT * FROM rental ORDER BY return_date DESC LIMIT 5;

```

---

### Задание 4

Одним запросом получите активных покупателей, имена которых Kelly или Willie.

Сформируйте вывод в результат таким образом:

    - все буквы в фамилии и имени из верхнего регистра переведите в нижний регистр,-
    - замените буквы 'll' в именах на 'pp'.

```

SELECT customer_id, REPLACE(LOWER(first_name), 'll', 'pp') FROM customer WHERE active = 1 AND first_name IN ('Kelly','Willie');

```

---



