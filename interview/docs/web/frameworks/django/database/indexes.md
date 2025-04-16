# Indexes 

**Індекси** - покращують швидкість вибірки (`SELECT`) для великих таблиць.

**2.1 Single-column Index (`db_index=True`)**

```python 
class User(models.Model):
  email = models.CharField(max_length=255, unique=True, db_index=True)
```

Якщо поле часто використовується в **WHERE**, додавання `db_index=True` допомогає.

**2.2 Використання `Meta.indexes`**

```python 
from django.db.models import Index 

class Order(models.Model):
  created_at = models.DateTimeField(auto_now_add=True)

  class Meta:
    indexes = [
      Index(fields=["created_at"]), # Індекс для швидкого пошуку за датою
    ]
```

**2.3 Composite Index**

```python 
class Product(models.Model):
  category = models.CharField(max_length=100)
  price = models.DecimalField(max_digits=10, decimal_places=2)

  class Meta:
    indexes = [
      Index(fields=["category", "price"]), # Індекс на дві колонки
    ]
```

**2.4 Primary Key (PK) Index**
Кожна модель у Django має первинний ключ (`PRIMARY KEY`), який автоматично створює унікальний індекс.

```python 
class User(models.Model):
  id = models.AutoField(primary_key=True) # PRIMARY KEY сворює індекс 
```

- **Дозволяє швидкий пошук за `id`**

**2.5 Unique Index**
Якщо поле має обмеження `unique=True`, Django автоматично створює унікальний індекс 
```python 
class User(models.Model):
  email = models.CharField(max_length=255, unique=True) # Унікальний індекс 
```

- **Гарантує унікальність значень і пришвидшує пошук**.

**2.6 Partial Index (Частковий індекс)**
Дозволяє створювати індекси **лише для певних значень**, що економить місце

```python 
from django.contrib.posgres.indexes import Index 


class Order(models.Model):
  status = models.CharField(max_length=20)

  class Meta:
    indexes = [
      Index(fields=["status"], name="copleted_orders_idx", condition=models.Q(status="completed"))
    ]
```
- **Економить місце, бо індексує тільки `status="completed`**.

***Спеціалізовані індекси PostgreSQL у Django***

**2.1 `GIN` Index (Generalized Inverted Index)**
Корисний для `JSONField`,`ArrayField`,`full-text search`

```python 
from django,contrib.postgres.indexes import GinIndex
from django.db.models import JSONField


class Log(models.Model):
  data = JSONField()

  class Meta:
    indexes = [
      GinIndex(fields=["data"]) # GIN індекс для JSON поля 
    ]
```

- **Прискорює пошук по JSON-данних**

**2.2 `B-Tree` Index (Стандартний індекс)**
За замовченням, `db_index=True` у Django використовує **B-Tree**, що підходить для `=` та `BETWEEN`.

```python 
class Transaction(models.Model):
  created_at = models.DateTimeField(db_index=True) # B-Tree індекс 
```

- **Прискорює запити `WHERE created_at > "2024-01-01"`**

**2.3 `BRIN` Index (Block Range Index)**
Добре підходить для **великих таблиць із часовими мітками**

```python 
from django.contrib.postgres.indexes import BrinIndex 


class Event(models.Model):
  timestamp = models.DateTimeField()

  class Meta:
    indexes = [
      BrinIndex(fields=["timestamp"]),
    ]
```
 - **Добре працює на дуже великих таблицях із послідовними значеннями**

**2.4 `Hash` Index**
Призначений для швидкого **пошуку по рівності** (`=`)

```python 
from django.db.models import Index 


class User(models.Model):
  email = models.CharField(max_length=255, unique=True)

  class Meta:
    indexes = [
      Index(fields=["email"], name="email_hash_idx", opclasses=["hash"]),
    ]
```
 - **Швидкий `WHERE email = "test@example.com"`, але не підтримує `>` та `<`**.
