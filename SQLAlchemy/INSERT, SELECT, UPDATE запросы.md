[[SQLAlchemy]]

Добавить записи в таблицу
```python
from models import User
from database import session_factory

async def add_user():
	async with session_factory() as session:
		user1 = User(username="Vanya")
		user2 = User(username="Vasya")
		session.add_all([user1, user2])
		# flush отправляет запрос в базу данных
		# После flush каждый из работников получает первичный ключ id, который отдала БД
		await session.flush()
		await session.commit()
```

Получить данные из БД
```python
async def get_user():
	async with session_factory() as session:
		query = select(User)
		result = await session.execute(query)
		users = result.scalars().all()
```

Обновить данные в БД
```python
async def update_user(user_id: int = 2, new_username: str = "Anton"):
	async with session_factory() as session:
		user = session.get(User, user_id)
		user.username = new_username
		# refresh нужен, если мы хотим заново подгрузить данные модели из базы.
		# Подходит, если мы давно получили модель и в это время
		# данные в базе данных могли быть изменены
		session.refresh(user)
		await session.commit()
```

