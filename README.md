# Домашнее задание к занятию «Индексы» - Костюк Денис

## Задание 1
Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

### Ответ 1

SELECT sum(index_length) * 100 / sum(data_length) as "% индексов"  
FROM INFORMATION_SCHEMA.TABLES  
WHERE TABLE_SCHEMA = 'sakila';  

## Задание 2

Выполните explain analyze следующего запроса:  
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)  
from payment p, rental r, customer c, inventory i, film f  
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id  
•	перечислите узкие места;  
•	оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.  

### Ответ 2

Анализ показал, что время выполнения исходного SQL-запроса составляет 10719 м.сек.  
Узкое место = SQL-запрос обрабатывает лишняя таблица: film.  
SQL-запрос после оптимизации:  

select concat(c.last_name, ' ', c.first_name), sum(p.amount)  
from payment p  
join rental r on p.payment_date = r.rental_date  
join customer c on r.customer_id = c.customer_id  
join inventory i on i.inventory_id = r.inventory_id  
where date(p.payment_date) >= '2005-07-30' AND date(p.payment_date) < DATE_ADD('2005-07-30', INTERVAL 1 DAY)  
GROUP BY c.customer_id;   

Скрин EXPLAIN ANALYZE:
![Скрин1-комментарий](https://github.com/denniskostyuk/index/blob/main/task-2.png)

