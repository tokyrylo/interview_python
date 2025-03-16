# Зрівняння класів

***1. Використання методів `__eq__`, `__ne__`, `__lt__`, `__gt__`, `__le__`, `__ge__`***

Python дозволяє перевизначити оператори порівняння (`==`, `!=`, `<`, `>`, `<=`, `=>`) через магічні методи.

**Приклад**:

```python 
class Car:
  def __init__(self, brand, price):
    self.brand = brand
    self.price = price 

  def __eq__(self, other):
    if isinstance(other, Car):
      return self.price < other.price 
    return NotImplemented # якщо порівнюють з іншим типом 

car1 = Car("toyota", 20000)
car2 = Car("toyota", 20000)
car3 = Car("BMW", 30000)

print(car1 == car2)  # True (бо однакові brand та prica)
print(car1 == car3) # False
print(car1 < car3) # True (бо 20000 < 30000)
```

 - Використання `__eq__` дозволяє порівнювати об'єкти за конкретними атрибутами.
 - `__lt__` (`<`) використовується для порівнянн за ціною.

***2. Використання `dataclasses`***

Модуль `dataclasses` автоматично додає методи порівняння.

**Приклад**:

```python 
from dataclasses import dataclass 

@dataclass 
class Car:
  brand: str 
  price: str 


car1 = Car("Toyota", 20000)
car2 = Car("Toyota", 20000)
car3 = Car("BMW", 30000)

print(car1 == car2) # True (автоматично генерується __eq__)
print(car1 < car3) # True (автоматично генерується __lt__)
```

***3. Порівняння за хешем (`__hash__`)***

Якщо клас має унікальні атрибути (наприклад, ID), можна визначити `__hash__`.

**Приклад**:

```python 
class User:
  def __init__(self, user_id, name):
    self.user_id = user_id 
    self.name = name 

  def __eq__(self, other):
    return isinstance(other, User) and self.user_id == other.user_id 

  def __hash__(self):
    return hash(self.user_id)

user1 = User(1, "Alice")
user2 = User(1, "Alice")
user3 = User(3, "Bob")

print(user1 == user2) # True (бо user_id однакові)
print(user1 == user3) # False 
```
 - Дозволяє використовувати об'єкти у множинах (`set`) та словниках (`dict`)

***4. Використання `functools.total_ordering`***

Що не перевизначити всі оператири, можна використовувати `@total_ordering`.

**Приклад**

```python 
from functools import total_ordering

@total_ordering
class Product:
  def __init__(self, name, price):
    self.name = name 
    self.price = price 

  def __eq__(self, other):
    return self.price == other.price 

  def __lt__(self, other):
    return self.price < other.price 

p1 = Product("Laptop", 1000)
p2 = Product("Tablet", 500)
p3 = Product("Laptop", 1000)


print(p1 == p3) # True 
print(p2 < p1) # True 
print(p1 > p2) # True (автоматично додається __gt__)
```

 - `@total_ordering` дозволяє визначити тільки `__eq__` і `__lt__`, а решту методів (`>`, `<=`, `>=`) Python генерує автоматично.

***Висновки***

- **Для простого порівняння (`==`)** - перевизначаємо `__eq__`.
- **Для повного порядку (`>`, `<`, `<=`, `>=`)** - перевизначаємо `__lt__` і `@total_ordering`.
- **Для автоматичного порівняння** - використовуємо `@dataclass`.
- **Для унікальних об'єктів** - додаємо `__hash__`.
