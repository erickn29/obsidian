[[SQLAlchemy]]

```python
def select_orders_avg_cost(distributor: str = "ufarma"):
	with session_factory() as session:
		query = (
			select(
				Order.cost,
				(
					func.avg(Order.cost)  # Встроеная фу-ия
					.cast(Integer)  # Приведение типа к int
					.label("avg_cost")  # Называем столбец
				),
			)
			.filter(and_(  # SQL оператор AND, еще есть OR (or_)
				Order.distributor.contains(distributor),  # LIKE %string%
				Order.cost > 1000,
			))
			.group_by(Order.cost)  # GROUP_BY
			.having(func.avg(Order.cost) > 2000)  # Условие
		)
		res = session.execute(query)
		result = res.all()  # Список кортежей
		print(result[0].avg_cost)
```
