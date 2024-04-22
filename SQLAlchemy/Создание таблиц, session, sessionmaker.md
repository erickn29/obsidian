В [[SQLAlchemy]] `` `Session` `` - это объект, который представляет соединение с базой данных и управляет транзакциями, запросами и объектами модели. Он является центральным элементом ORM (Object-Relational Mapping) в SQLAlchemy.

`` `Session` `` выполняет следующие функции:

1. Управление соединением с базой данных: `` `Session` `` отвечает за открытие и закрытие соединений с базой данных, а также за управление пулом соединений, если он используется.
2. Управление транзакциями: `` `Session` `` позволяет начинать, фиксировать и откатывать транзакции. Каждый метод запроса выполняется в рамках текущей активной транзакции.
3. Кэширование объектов: `` `Session` `` поддерживает кэш объектов, который хранит объекты модели, загруженные из базы данных. Это позволяет избежать повторных запросов к базе данных при повторном доступе к уже загруженным объектам.
4. Сохранение и загрузка объектов: `` `Session` `` позволяет сохранять объекты модели в базе данных и загружать объекты из базы данных по их идентификаторам или другим условиям.
5. Выполнение запросов: `` `Session` `` предоставляет методы для выполнения различных типов запросов к базе данных, таких как выборка, вставка, обновление и удаление данных.

Чтобы использовать `` `Session` `` в SQLAlchemy, необходимо создать экземпляр класса `` `Session` `` и настроить его для работы с конкретной базой данных. Обычно это делается с помощью фабрики сессий, которая создает и возвращает объекты `` `Session` `` при необходимости. Например:
```python
from sqlalchemy.ext.asyncio import create_async_engine
from sqlalchemy.orm import sessionmaker 

engine = create_async_engine('postgresql://user:password@localhost/mydatabase') session = sessionmaker(bind=engine)
```

Удобнее использовать контекстный менеджер, который возвращает экземпляр сессии для дальнейшей с ним работы

```python
async def get_session():
	async with session() as session:
		yield session

session = get_session()
query = select(UserTable).where(UserTable.id == 1)
result = await session.execute(query)
```

Создание таблиц

```python
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import Mapped, mapped_column
import enum
from typing import Annotated

Base = declarative_base()  # Базовый класс таблиц

intpk = Annotated[int, mapped_column(primary_key=True)]
created_at = Annotated[
				datetime, 
				mapped_column(server_default=text("TIMEZONE('utc', now())")),
			]
updated_at = Annotated[
				datetime,
				mapped_column(
					server_default=text("TIMEZONE('utc', now())"),
					onupdate=datetime.utcnow,
				)
			]

class Role(enum.Enum):
	user = "user"
	admin = "admin"

class User(Base):
	__tablename__ = "user"
	id: Mapped[intpk]
	username: Mapped[str] = mapped_column(String(32), unique=True)
	email: Mapped[str | None]
	role: Mapped[Role]  # Enum/Choices
	created_at: Mapped[created_at]
	updated_at: Mapped[updated_at]

class Project(Base):
	__tablename__ = "project"
	id: Mapped[intpk] = mapped_column(primary_key=True)
	title: Mapped[str] = mapped_column(String(32))
	owner_id: Mapped[int] = mapped_column(
		ForeignKey("users.models.User.id", ondelete="CASCADE"),
	)
	created_at: Mapped[created_at]
	updated_at: Mapped[updated_at]
```
