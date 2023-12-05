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
Узкое место = SQL-запрос обрабатывает лишние таблицы: inventory, rental и film.  
SQL-апрос после оптимизации:  

select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id)  
from payment p, customer c  
where date(p.payment_date) = '2005-07-30' and p.customer_id = c.customer_id;  

Время выполнения оптимизированного SQL-запроса = 18,9 м.сек.  
