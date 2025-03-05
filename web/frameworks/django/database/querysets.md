# Queryset 

**Queryset** - це набір об'єктів, отриманих із бази даних через Django ORM.
Основні операції у Django:

```python
Post.objects.all()

Post.objects.filter(author="John")

Post.objects.get(id=1)

Post.objects.filter(author="John", status="published")

from django.db.models import Q 


Post.objects.filter(Q(author="John") | Q(status="published"))
```

***Методи QuerySet***

| Методи | Опис |
| -------------- | --------------- |
| `.all()` | Отримати всі записи |
| `.filter(**kwargs)` | Фільтрує об'єкти (аналог WHERE) |
| `.exclude(**kwargs)` | Виключає об'єкти |
| `.get(**kwargs)` | Отримати один об'єкт (повертає помилку, якщо немає або більше одного) |
| `.order_by("field")` | Сортування результатів |
| `.values("field")` | Повертає словники замість об'єктів |
| `.distinct()` | Видаляє дублікати |
| `.count()` | Повертає кількість записів |
| `.first()` / `.last()` | Повертає перший або останній запис |
| `.exists()` | Перевіряє, чи є запис |

**QuerySet не виконує запит одразу, він "ледачий" (lazy)** - виконується лише при зверненні до нього.

**QuerySet - це основний механізь Django ORM для роботи з базою даних**


