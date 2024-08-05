# Домашнее задание к занятию "`Индексы`" - `Никифоров Роман`

[Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1 

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

```
select sum(index_length) / SUM(data_length + index_length) * 100 result
from information_schema.TABLES
where table_schema = 'sakila';

```

---

### Задание 2

Выполните explain analyze следующего запроса:

```

select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id

```

-перечислите узкие места;
-оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.


explain analyze "до вмешательства":

```
-> Table scan on <temporary>  (cost=2.5..2.5 rows=0) (actual time=5656..5656 rows=391 loops=1)
    -> Temporary table with deduplication  (cost=0..0 rows=0) (actual time=5656..5656 rows=391 loops=1)
        -> Window aggregate with buffering: sum(payment.amount) OVER (PARTITION BY c.customer_id,f.title )   (actual time=2383..5457 rows=642000 loops=1)
            -> Sort: c.customer_id, f.title  (actual time=2383..2442 rows=642000 loops=1)
                -> Stream results  (cost=22.1e+6 rows=16.3e+6) (actual time=0.553..1741 rows=642000 loops=1)
                    -> Nested loop inner join  (cost=22.1e+6 rows=16.3e+6) (actual time=0.547..1485 rows=642000 loops=1)
                        -> Nested loop inner join  (cost=20.5e+6 rows=16.3e+6) (actual time=0.542..1305 rows=642000 loops=1)
                            -> Nested loop inner join  (cost=18.8e+6 rows=16.3e+6) (actual time=0.535..1083 rows=642000 loops=1)
                                -> Inner hash join (no condition)  (cost=1.61e+6 rows=16.1e+6) (actual time=0.52..42 rows=634000 loops=1)
                                    -> Filter: (cast(p.payment_date as date) = '2005-07-30')  (cost=1.68 rows=16086) (actual time=0.0443..7.38 rows=634 loops=1)
                                        -> Table scan on p  (cost=1.68 rows=16086) (actual time=0.0306..5.39 rows=16044 loops=1)
                                    -> Hash
                                        -> Covering index scan on f using idx_title  (cost=112 rows=1000) (actual time=0.045..0.373 rows=1000 loops=1)
                                -> Covering index lookup on r using rental_date (rental_date=p.payment_date)  (cost=0.969 rows=1.01) (actual time=0.00107..0.0015 rows=1.01 loops=634000)
                            -> Single-row index lookup on c using PRIMARY (customer_id=r.customer_id)  (cost=250e-6 rows=1) (actual time=185e-6..209e-6 rows=1 loops=642000)
                        -> Single-row covering index lookup on i using PRIMARY (inventory_id=r.inventory_id)  (cost=250e-6 rows=1) (actual time=119e-6..143e-6 rows=1 loops=642000)

```
Узкое место большой объем данных, как следствие долгое выполнение запроса - 6 с небольшим секунд. Часто в службах зависимых от бд выполняемый больше 3 секунд запрос к БД уже "триггерит" как slow query - в связи с чем оптимизация тут кажется необходима.

    Добавим индексы idx_payment_date
    Customer и inventory достаем с помощью join
    f.title and film f не нужны больше
    переписан фильтр p.payment_date >= '2005-07-30' and p.payment_date < date_add('2005-07-30', interval 1 day);

```
select distinct concat (c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id)
from payment p
join rental r on p.payment_date = r.rental_date
join customer c on r.customer_id = c.customer_id
join inventory i on r.inventory_id = i.inventory_id
where p.payment_date >= '2005-07-30' and p.payment_date < date_add('2005-07-30', interval 1 day);
```
