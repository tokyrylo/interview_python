# Агрегації у Django ORM

**Агрегація** - це процес обчислення **зведених даних** (наприклад, сума, середнє, мінімум, максимум, кількість) на основі набору записів у базу даних.
сума. У `Django ORM` агрегації використовуються для отримання підсумкових значень з таблиць бази даних. Наприклад:

 - підрахунок загальної кількості замовлень,
 - знаходження середньої ціни товарів,
 - визначення максимальної зарплати працівника,
 - отримання кількості унікальних користувачів.

***Де використовується агрегація?***

1. **Вибірка загального значення для всієї таблиці** - `aggregate()`
2. **Обчислиння значення для кожного запису** - `annotate()`
3. **Групування даних** - `values().annotate()`
4. **Фільтрація перед агрегацією** - `filter=Q(...)`
5. **Обчислення унікальних значень** - `distinct=True`

***Основні агрегатні функції***

| Функція        | Опис                  |
| -------------- | --------------------- |
| `Count()`      | Підраховує кількість записів |
| `Sum()`      | Обчислює суму значень        |
| `Avg()`        | Обчислює середнє значення |
| `Min()` | Знаходить мінімальне значення |
| `Max()` | Знаходить максимальна значення  |
| `StdDev()` | Стандартне відхилення |
| `Variance()` | Дисперсія |

Всі ці функції знаходяться у `django.db.models`.

```python
from django.db.models import Count, Sum, Avg, Min, Max
```

***`aggregate()`: Загальні агрегатні підрахунки***

Цей метод поверне **один результат** для всієї таблиці.

**Приклад:** Підрахуємо загальну кількість замовлень та середню ціну:

```python
from django.db.models import Avg, Count

from myapp.models import Order


result = Order.objects.aggregate(
  total_orders=Count("id"),
  average_price=Avg("price"),
)

print(result) # {"total_orders": 10, "average_price": 150.75}
```
- `total_orders=Count("id")` - загальна кількість записів у таблиці
- `average_price=Avg("price")` - середня ціна товару

***`annotate()`: Агрегація для кожного запису:***

На відміну від `aggregate()`, метод `annotate()` повертає **агреговане значення для кожного об'єкта** в запиті.
**Приклад:** Отримати кількість замовлень для кожного користувача:

```python
from myapp.models import User, Order 
from django.db.models import Count

users = User.objects.annotate(total_orders=Count("order"))

for user in users:
  print(f"{user.name}: {user.total_orders}")

```

- Це поверне список користувачів, кожен з яких матиме додаткове поле `total_orders` - кількість його замовлень.

***Групування за допомогою `annotate()` + `values()`***

Якщо ми хочемо зробити групування (`GROUP BY` у SQL), використовуємо `values()`.
**Приклад:** Групуємо замовлення за статусом і рахуємо їх кількість:

```python
from django.db.models import Count

Order.objects.values("status").annotate(total_orders=Count("id"))
```

- Це поверне словники:

```python
[
  {"status": "completed", "total_orders": 15},
  {"status": "pending", "total_orders": 8},
]
```
***Приклади використовування інших агрегатних функцій***
**Мінімальна і максимальна ціна товарів:**

```python
from django.db.models import Min, Max


result = Product.objects.aggregate(
  min_price=Min("price"),
  max_price=Max("price")
)

print(result) # {"min_price": 50.0, "max_price": 1000.0}
```

**Сума всіх замовлень для кожного користувача:**

```python
from django.db.models import Sum

users = User.objects.annotate(total_spend=Sum("orders__total_price"))
```

**Знаходження середнього рейтингу продукту:**

```python
from django.db.models import Avg


Product.objects.aggregate(avg_rating=Avg("reviews__rating"))
```

***Використання `F()` у агрегації***

Можна використовувати `F()` для роботи з полями.
**Приклад:** Розрахуємо загальну вартість замовлення з урахуванням кількості товарів:

```python
from django.db.models import F, Sum

order = Order.objects.annotate(total_price=Sum(F("quantity") * F("unit_price")))
```

***Фільтрація в агрегації***

Можна передавати умови в агрегатні фунеції.
**Приклад:** Середня ціна товарів дорожчих за 100:

```python
Product.objects.aggregate(avg_expensive=Avg("price", filter=Q(price__qt=100)))
```
***Використання `distinct=True` у `Count()`***
Щоб підрахувати **унікальні значення**, використовуйте `distinct=True`.
**Приклад:** Підрахунок унікальних користувачів, які зробили замовлення:

```python
Order.objects.aggregate(unique_users=Count("user", distinct=True))
```

***Висновок:***

- `aggregate()` - повертає **одне значення** для всієї таблиці.
- `annotate()` - повертає **агреговане значення** для кожного запису.
- `values()` + `annotate()` - дозволяє **групувати результати** (аналог `GROUP BY` в SQL)
- **Можна використовувати фільтри (`filter=Q(...)`)**, `F()` **вирази та** `distinct=True`

