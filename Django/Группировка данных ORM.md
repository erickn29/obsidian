Для группировки данных по пользователю, подсчета суммы процентов и количества записей можно воспользоваться [[django]] [[ORM]]. Для этого можно использовать агрегационные функции и метод annotate(). Ниже приведен пример кода, который может помочь вам решить задачу:

```python
from django.db.models import Sum, Count

# Импортируем модель Incident
from app.models import Incident

# Группировка данных по пользователю, подсчет суммы процентов и количества записей
incident_data = Incident.objects.values('user').annotate(total_percent=Sum('percent'), total_count=Count('id'))

for data in incident_data:
    user_id = data['user']
    total_percent = data['total_percent']
    total_count = data['total_count']
    
    # Здесь можно использовать полученные данные для дальнейшей обработки
```

В данном примере мы используем метод values() для группировки данных по полю 'user', затем метод annotate() для подсчета суммы процентов (total_percent) и количества записей (total_count) для каждого пользователя. Вы можете дальше обрабатывать эти данные в цикле или передать их в шаблон для отображения.