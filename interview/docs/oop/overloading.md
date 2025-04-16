# Class overloading

В Python немає класичної перевантаження матодів (`overloading`), як у Java або С++, де можна визначати кілька методів з однаковими іменами, але з різними параметрами. Однак у Python можна імітувати перевантаження методів за допомогою механізмів, таких як:

***1. Перевантаження методів через аргументи за замовченням***

Python дозволяє задавати значення аргументів за замовченням, щоб метод міг працювати з різною кількістю параметрів:

```python 
class Example:
  def greet(self, name="Guest"):
    print(f"Hello, {name}!")

obj = Example()
obj.greet() # Hello, Guest! 
obj.greet("Alice") # Hello, Alice!
```

***2. Використання `*args` та `**kwargs`***

Це дозволяє писати методи, які приймають змінну кількість аргументів:

```python 
class Calculator:
  def add(self, *args):
    return sum(args)


calc = Calculator()
print(calc.add(1, 2)) # 3 
print(calc.add(1, 2, 3, 4)) # 10
```

***3. Перевантаження методів через `@singledispatch`***

Python підтримує перевантаження функцій через `functools.singledispatch`, яке дозволяє визначати різні реалізації залежно від типу аргументу:

```python 
from functools import singledispatch


@singledispatch
def process(value):
  raise NotImplementedError("Unsupported type")

@process.register(int)
def _(value):
  print(f"Processing integer: {value}")

@process.register(str)
def _(value):
  print(f"Processing string: {value}")

process(42) # Processing integer: 42 
process("text") # Processing string: text
```

***4. Перевантаження операторів (`__add__`, `__mul__`, і т.д)***

Python дозволяє перевантажувати оператори, наприклад:

```python 
class Vector:
  def __init__(self, x, y):
    self.x = x 
    self.y = y 

  def __add__(self, other):
    return Vector(self.x + other.x, self.y + other.y)

  def __repr__(self):
    return f"Vector({self.x}, {self.y})"

v1 = Vector(1, 2)
v2 = Vector(3, 4)

print(v1 + v2) # Vector(4, 6)
```
***5. Метапрограмування (Метакласи)***

Якщо потрібно більш складне перевантаження, можна використовувати метакласи:

```python 
class Meta(type):
  def __call__(cls, *args, **kwargs):
    print("Creating instance...")
    return super().__call__(*args, **kwargs)


class Example(metaclass=Meta):
  def __init__(self, value):
    self.value = value 


obj = Example(10) # Creating instance...
```
Python дає гнучкі способи реалізації поведінки, схожої на переватаження методів у традиційних мовах ООП.
