
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

[[SQL]] [[linux]]