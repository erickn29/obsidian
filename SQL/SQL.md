## Введение

**SQL** (Structured Query Language) - это язык программирования, который используется для управления и обработки данных в реляционных [[базах данных]]. 

**База данных** - организованный набор данных, который хранится и управляется СУБД.

**СУБД** (Система управления базами данных) - программа для создания и управления базами данных.

**Кластер** - набор баз данных на одном компьютере, который управляется конкретной запущенной на этом компьютере программой-сервером [[PostgreSQL]].

**PostgreSQL** - объектно-реляционная СУБД с открытым исходным кодом. 

В реляционных СУБД данные организованы в таблицы, которые состоят из строк и колонок. Строки - записи (фактические данные), колонки - атрибуты данных. Принципы работы реляционных СУБД основываются на реляционной алгебре (основана на понятии отношения (relation)). 
Таблица в которой хранятся данные есть *отношение*, строка в таблице называется *кортежем*, *атрибут* - колонка, которая имеет имя и множество возможных значений.


## Установка PostgreSQL в Linux из пакетного менеджера

Обновление репозиториев и поиск в них чего-то похожего на `postgres`:

```bash
sudo apt update
apt search postgres
```

Последовательно выполняем операции из документации:

```bash
# Import the repository signing key:
sudo apt install curl ca-certificates
sudo install -d /usr/share/postgresql-common/pgdg
sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc

# Create the repository configuration file:
sudo sh -c 'echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

# Update the package lists:
sudo apt update

# Install the latest version of PostgreSQL:
# If you want a specific version, use 'postgresql-16' or similar instead of 'postgresql'
sudo apt -y install postgresql
```

**psql** - утилита, с помощью который можно подключиться к серверу postgres

```bash
psql -h localhost -U postgres
```

`-h` - хост, где расположена СУБД
`-U` - пользователь

Сбросить пароль для юзера `postgres` и зайти под ним в СУБД:

```bash
sudo passwd postgres
su - postgres
psql
```

Создадим роль и базу данных (поговорим подробнее об этом позже, а сейчас надо просто создать), выполняем в `psql`:

```sql
CREATE ROLE new_user WITH LOGIN PASSWORD 'mycoolp@ssword';

CREATE DATABASE new_db
    WITH
    TEMPLATE=template0
    ENCODING='UTF8'
    LC_COLLATE='ru_RU.UTF-8'
    LC_CTYPE='ru_RU.UTF-8'
    owner new_user;
```

Чтобы посмотреть, где лежат данные наших баз данных (если интересно), выполняем в `psql`:

```sql
show data_directory;
```

## Первичный ключ и внешний ключ

**Первичный ключ (Primary Key, PK)** - колонка, которая однозначно идентифицирует конкретную запись в таблице. Первичный ключ должен быть заполнен и быть уникальным для конкретной таблицы. Может быть строкой, числом, uuid. Может быть составным (состоять из двух и более колонок). 

*Суррогатный PK* - тот, который не имеет физического смысла (пример, `author_id=33`).
*Естественный ключ* - имеет физический смысл (пример, `email=123@321.com`)

**Внешний ключ (Foreign Key, FK)** - колонка в таблице, значение которой соответствует значению первичного ключа в другой таблице.


## DDL, DML, DCL, TCL и прочий БДСМ

SQL — Structured Query Language, язык структурированных запросов. Все команды SQL можно разделить на 4 группы:

- DDL — Data Definition Language, язык определения данных
- DML — Data Manipulation Language, язык управления данными
- DCL — Data Control Language, язык управления доступом к данным
- TCL — Transaction Control Language, язык управления транзакциями


## Создание таблиц в БД

Создаём таблицу авторов:

```sql
create table author (
    author_id bigint generated always as identity primary key,
    name varchar(150) not null check (length(name) >= 3),
    description text check (length(description) >= 30)
);
```

`author` - название таблицы

`author_id` - название колонки
`bigint` - тип данных
`generated always as identity` - всегда генерируется как идентификатор (уникально!)
`primary key` - указание на то, что колонка является первичным ключом

`not null` - должно быть заполнено
`check (length(name) >= 3)` - проверка длины

Почему название в единственном числе - логичнее, например при соединении таблиц писать author.author_id потому что идентификатор есть у автора, а не у авторов. Таблица будет маппиться на какой либо класс в Python, который принято называть так же в единственном числе.

PK рекомендуется называть *сущность_id* (book.book_id), может упростить некоторые запросы.

Создаём таблицу для хранения книжных категорий:

```sql
create table book_category (
    category_id int generated always as identity primary key,
    name varchar(150) not null check (length(name) >= 2)
);
```

Создаём таблицу для книг:

```sql
create table book (
    book_id bigint generated always as identity primary key,
    name varchar(255) not null check (length(name) >= 2),
    author_id bigint not null references author(author_id),
    description text check (length(description) >= 30),
    cover varchar(255),
    category_id int not null references book_category(category_id)
);
```

**Важно**: типы внешнего ключа и первичного ключа, на который он ссылается, должны совпадать.

Удаляем и пересоздаём таблицы (просто для того, чтобы показать возможность удаления таблиц):

```sql
drop table if exists book_category, author, book;

create table author (
    author_id bigint generated always as identity primary key,
    name varchar(150) not null check (length(name) >= 3),
    description text check (length(description) >= 30)
);

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
    category_id int not null references book_category(category_id)
);
```

Создание временной таблицы, вставка в неё двух строк и выборка данных этой таблицы:

```sql
create temp table something (id serial, name text);

insert into something (name) values ('hello');
insert into something (name) values ('dratuti');

select * from something;
```