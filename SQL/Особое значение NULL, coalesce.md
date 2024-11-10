
Создадим временную таблицу `author_without_checks`, которая такая же, как и наша таблица `author`, но из неё мы уберём ограничения `check`, которые проверяют минимальную длину строк:

```sql
create temp table author_without_checks (
    author_id bigint generated always as identity primary key,
    name varchar(150) not null,
    description text
);
```

Вставим автора в эту временную таблицу без указания полей `author_id` и `description`:

```sql
insert into author_without_checks (name) values (
    'какой-то автор'
);

select * from author_without_checks; -- 'какой-то автор', description null
```

Некоторые поля мы при создании таблиц указываем как `NOT NULL`, они не могут хранить в себе значения `NULL`. Поле `name` указано как `NOT NULL`, попробуем вставить туда `NULL`:

```sql
insert into author_without_checks (name) values (null);
```

Этот запрос упадёт с ошибкой.

Добавим ещё одного автора, для которого значение поле `description` будет пустой строкой:

```sql
insert into author_without_checks (name, description) values ('и ещё один автор', '');

select * from author_without_checks;
```

Мы видим, что `NULL` и пустая строка отображаются по-разному у нас в DBeaver, и в psql тоже.

Давайте достанем всех авторов, у которых описание это пустая строка:

```sql
select * from author_without_checks where description='';
```

Достаётся только одна строка. А как достать строку, для которой это поле имеет значение `NULL`?

```sql
select * from author_without_checks where description=NULL;
```

Не досталась ни одна строка почему-то. А почему так? У нас же есть одна строка такая, совершенно точно, мы её только что вставили в эту таблицу.

Дело в том, что при сравнении любого значения с `NULL` результат тоже `NULL`:) Потому что `NULL` это отсутствие значения, это неизвестность. А при сравнении с неизвестностью неизвестность и получаем.

```sql
select 2=2;
select 2=100;
select 2>3;
select 2<3;

select null=null;
select 2>null;
select 2<null;
select 2=null;
```

Ну, звучит, предположим, может и логично, но как же нам достать строку, для которой значение нужного поля имеет значение `NULL`? Для этого придуман специальный синтаксис `IS NULL`:

```sql
select * from author_without_checks where description is null;

select null is null;
select '' is null;
select 0 is null;
```

Отлично!

Иногда нам нужно в результатах преобразовать `NULL` в какое-то другое значение. Это можно сделать с помощью встроенной функции `coalesce`:

```sql
select
    author_id,
    name,
    coalesce(description, 'не заполнено') as description
from author_without_checks;
```

Обратите внимание — чтобы результат выполнения функции `coalesce` в выдаче был назван не `coalesce`, а `description`, мы используем alias, то есть псевдоним, что то же самое.

Также обратите внимание, что все строки с заполненным полем `description` никак не обработаны функцией `coalesce`, в том числе пустая строка осталась именно пустой строкой. Потому что пустая строка это не `NULL`, `NULL` это отсутствие значения, а пустая строка это конкретное значение.

Это важно понять. Такие особые значения есть не только в базах данных, но и почти в каждом ЯП. Важно понимать, что такое `NULL`. Поиграйтесь с этими примерами обязательно — как, впрочем, и со всеми остальными примерами, которые я здесь показываю, конечно.

[[SQL]]