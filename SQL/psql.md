клиентская программа для работы с postgres

Изменить пароль для юзера postgres
```bash
sudo passwd postgres
```

Зайти под юзером postgres
```bash
su - postgres
```

```bash
psql
```

Подключиться к серверу:
```bash
psql -h localhost -U postgres
```

Подключиться к БД:
```bash
psql -h localhost -U aadmin -d web_builder
```

Создать БД ([[DDL]])
```sql
create database test_db with 
template=template0 
encoding='UTF8' 
lc_collate='ru_RU.UTF-8' 
lc_ctype='ru_RU.UTF-8' 
owner aadmin;
```

Посмотреть все базы в psql
```bash
\l
```

Переключиться на другую БД в psql
```bash
\c db_name
```


Создание таблиц
```sql
create table author ( 
author_id bigint generated always as identity primary key, 
name varchar(150) not null check (length(name) >= 3), 
description text check (length(description) >= 30) 
);
# наименование тип опции

create table book_category ( 
category_id int generated always as identity primary key, 
name varchar(150) not null check (length(name) >= 2) 
);

create table book ( 
book_id bigint generated always as identity primary key, 
name varchar(255) not null check (length(name) >= 2), 
author_id bigint not null references author(author_id), 
description text check (length(description) >= 30), 
cover varchar(255), 
category_id int not null references book_category(category_id) );
# references таблица(поле_ид)
```

Изменение таблиц
```sql
ALTER TABLE author DROP COLUMN description;
ALTER TABLE products ADD CHECK (name <> '');
ALTER TABLE products ALTER COLUMN product_no SET NOT NULL;
ALTER TABLE products DROP CONSTRAINT some_name;
ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
ALTER TABLE products ALTER COLUMN price DROP DEFAULT;
ALTER TABLE products ALTER COLUMN price TYPE numeric(10,2);
ALTER TABLE products RENAME COLUMN product_no TO product_number;
ALTER TABLE products RENAME TO items;
```

Удаление таблиц
```sql
drop table if exists author, book;
```

Вставка данных в таблицу
```sql
insert into table_name (col1, col2) values ('value1', 'value2'), ('val2', 'val2')
```

Получить данные из таблицы
```sql
select * from author; или
table author;
```

```sql
select col1, col2 from table_name
```

Выборка из результатов функций:
```sql
select num from generate_series(1, 10) num; -- от 1 до 10 включительно

select d
from generate_series(
    '2023-01-01'::date,
    '2023-01-10'::date,
    '1 day'::interval
) d; 

select to_char(date, 'DD.MM.YYYY') date from generate_series('2020-01-01'::date, '2024-06-01'::date, '2 months'::interval) date;
```

Выборка из временных value
```sql
select movie, imdb_rating, year from (values  
('The Shawshank Redemption', 9.3, 1994),  
('The Godfather ', 9.2, 1972),  
('The Dark Knight', 9.1, 2008),  
('Inception', 8.8, 2010))  
t(movie, imdb_rating, year);
```

