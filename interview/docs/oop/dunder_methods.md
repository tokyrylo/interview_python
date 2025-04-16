# Магічні методи (Dunder methods) в ООП Python

Магічні методи (або "dunder methods", від double underscore) - це спеціальні методи, які починаються і закінчиться підкресленнями (`__`), і які Python викликає автоматично в певних ситуаціях.

  - Вони використовуються для перевизначеняя поведінки класу, наприклад, роботи операторів (`+`, `-`, `==`), перетворення в рядок, обробки доступу до атрибутів і багато іншого.

***1. Ініціалізація та створення об'єктів***

| Метод | Опис |
| -------------- | --------------- |
| `__init__(self, ...)` | Конструктор, викликається при створенні об'єктів. |
| `__new__(cls, ...)` | Викликається перед `__init__`, використовується для контролю створення екземпляру. |
| `__del__(self)` | Деструктор, викликається при видаленні об'єктів (не завжди надійними). |

**Приклад**

```python 
class Person:
  def __init__(self, name):
    self.name = name # Ініціалізація

  def __del__(self):
    print(f"Об'єкт {self.name} знищено")

p = Person("John")
del p # Викличе __del__
```

***2. Представлення об'єкта***

| Метод | Опис |
| -------------- | --------------- |
| `__str__(self)` | Викликається `str(obj)`, `print(obj)`, повертає рядкове представлення. |
| `__repr__(self)` | Виклитається `repr(obj)`, повертає строкове представлення для відладки. | 
| `__format__(self, format_spec)` | Викликається при `format(obj, spec)`. |

**Приклад**

```python 
class Car:
  def __init__(self, brand, price):
    self.brand = brand 
    self.price = price 

  def __str__(self):
    return f"{self.brand} - ${self.price}"

  def __repr__(self):
    return f"Car('{self.brand}', {self.price})"

c = Car("Toyota", 20000)
print(str(c)) # Викличе __str__
print(repr(c)) # Викличе __repr__
```
***3. Арифметичні оператори***

| Метод | Опис |
| -------------- | --------------- |
| `__add__(self, other)` | `+` (додавання) |
| `__sub__(self, other)` | `-` (віднімання) |
| `__mul__(self, other)` | `*` (множення) |
| `__truediv__(self, other)` | `/` (ділення) |
| `__floordiv__(self, other)` | `//` (цілочисельне ділення) |
| `__mod__(self, other)` | `&` (остача від ділення) |
| `__pow__(self, other)` | `**` (піднесення до степеня) |


**Приклад**

```python 
class Money:
  def __init__(self, amount):
    self.amount = amount 

  def __add__(self, other):
    return Money(self.amount + other.amount)

  def __sub__(self, other):
    return Money(self.amount - other.amount)

  def __str__(self):
    return f"${self.amount}"

m1 = Money(100)
m2 = Money(50)

print(m1 + m2) #  Викличе __add__
print(m1 - m2) #  Викличе __sub__
```

***4. Оператори порівняння***

| Метод | Опис |
| -------------- | --------------- |
| `__eq__(self, other)` | `==` (рівність) |
| `__ne__(self, other)` | `!=` (нерівність) |
| `__lt__(self, other)` | `<` (менше) |
| `__le__(self, other)` | `<=` (менше або рівно) |
| `__gt__(self, other)` | `>` (більше) |
| `__ge__(self, other)` | `>=` (більше або равно) |

**Приклад**

```python 
class Product:
  def __init__(self, price):
    self.price = price 

  def __eq__(self, other):
    return self.price == other.price 

  def __lt__(self, other):
    return self.price < other.price 

p1 = Product(100)
p2 = Product(200)

print(p1 == p2) # Викличе __eq__
print(p1 < p2) # Викличе __lt__
```
***5. Робота з індексами та ітерацією***

| Метод | Опис |
| -------------- | --------------- |
| `__getitem__(self, key)` | `obj[key]` (отримання значення за ключем). |
| `__setitem__(self, key, value)` | `obj[key] = value` (зміна значення за ключем). |
| `__delitem__(self, key)` | `del obj[key]` (видалення елемента). |
| `__iter__(self)` | Повертає ітератор (`iter[obj]`). |
| `__next__(self)` | Викликається `next(obj)` для ітерації. |

**Приклад**

```python 
class MyList:
  def __init__(self, items):
    self.items = items 

  def __getitem__(self, index):
    return self.items[index]

  def __setitem__(self, index, value):
    self.item[index] = value

  def __iter__(self):
    return iter(self.items)

lst = MyList([1, 2, 3])
print(lst[1]) # Викличе __getitem__
lst[1] = 100 # Викличе __setitem__
print(list(lst)) # Викличе __iter__
```

***6. Контекстний менеджер (`with ... as`)***

| Метод | Опис |
| -------------- | --------------- |
| `__enter__(self)` | Викликається при вході в `with` |
| `__exit__(self, exc_type, exc_value, traceback)` | Викликається при виході з `with` |

**Приклад**

```python 
class FileManager:
  def __init__(self, filename):
    self.filename = filename

  def __enter__(self):
    self.file = open(self.filename, "w")
    return self.file 

  def __exit__(self, exc_type, exc_value, traceback):
    self.file.close()


with FileManager("text.txt") as f:
  f.write("Hello, world!")
```
 - Автоматично закриває файл при виході з `with`.


***Висновки***

 - Магічні методи дозволяють змінювати поведінку класів.
 - Використовується для операторів (`+`, `-`, `==`), роботи з індексами, ітерації та менеджерів контексту.
 - `dataclass` може автоматично створювати деякі з них (`__eq__`, `__repr__`). 




