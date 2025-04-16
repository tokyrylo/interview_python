***1. Види методів у класах (instance, class, static)***

У Python є три основних типи методів:

**1.1 Методів екземпляра (`instance methods`)**

Це стандартні методи, які отримують `self` як перший аргумент і працюють з конкретним екземпляром класу.

```python 
class Demo:
  def __init__(self, value):
    self.value = value # Атрибут екземпляра 

  def show(self): # Метод екземпляра
    return f"Value: {self.value}"

obj = Demo(10)
print(obj.show()) # "Value: 10"
```

**1.2 Методи класу (`@classmethod`)**

Методи класу працюють із самим класом, а не з конкретним екземпляром. Вони отримують `cls` як перший аргумент.

```python 
class Demo:
  counter = 0 # Атрибут класу 

  @classmethod
  def increment(cls):
    cls.counter += 1 
    return cls.counter 


print(Demo.increment()) # 1 
print(Demo.increment()) # 2 
```

**Використовується для роботи з атрибутами класу.**

***1.3 Статичні методи (`@staticmethod`)***

Статичні методи не отримують ні `self`, ні `cls`. Вони працюють як звичайні функції, але знаходяться всередині класу.

```python 
class Math:
  @staticmethod
  def add(x, y):
    return x + y 

print(Math.add(3, 4)) # 7 
```

**Використовується, коли метод не залежить від стану класу або об'єкту**

***2. Дескриптори: Як працюють `property`***

У python **всі атрибути - це об'єкти**, тому можна керувати їх доступом через **дескриптори (`property`)**

**Створення `property` в ручну**

```python 
class Demo:
  def __init__(self, value):
    self._value = value # Приватний атрибут 

  def get_value(self):
    return self._value 

  def set_value(self, new_value):
    if new_value < 0:
      raise ValueError("Значення не може бути від'ємним")
    self._value = new_value 

  value = property(get_value, set_value) # Дескриптор 
```
`property` дозволяє контролювати доступ до атрибутів.


**Короткий варіант через `@property`**

```python 
class Demo:
  def __init__(self, value):
    self._value = value 

  @property
  def value(self):
    return self._value 

  @value.setter
  def value(self, new_value):
    if new_value < 0:
      raise ValueError("Значення не може бути від'ємним")
    self._value = new_value

obj = Demo(10)
obj.value = 20 # Викличе setter 
print(obj.value) # Викличе getter 

```

`@property` **спрощує роботу з геттерами та сеттерами.**


***3. Поліморфізм: Duck Typing у Python***

Python підтримує **duck typing**, що означає, що типи об'єктів не важилві, якщо вони підтримують певний інтерфейс.

```python 
class Dog:
  def speak(self):
    return "Bark!"

class Cat:
  def speak(self):
    return "Meow!"

def make_sound(animal):
  return animal.speak() # Неважливо, який клас 

print(make_sound(Dog())) # "Bark!"
print(make_sound(Cat())) # "Meow!"
```
**Python не перевіряє типи, а лише те, чи має об'єкт необхідний метод.**

***4. Абстрактні класи та інтерфейси***

Якщо потрібно створити **інтерфейс**, можна використати `ABC` (Absract Base Class).

```python 
from abc import ABC, abstractmethod


class Animal(ABC):
  @abstractmethod
  def speak(self):
    pass # Повинен бути реалізований у дочірних класах 

class Dog(Animal):
  def speak(self):
    return "Bark!"

# obj = Animal() # Не може створювати об'єкти абстрактного класу 
dog = Dog()
print(dog.speak()) # Bark!
```
**Абстракні класи забезпечують конракт для дочірніх класів.**

***5. Динамічне додавання атрибутів і методів***

Python дозволяє **додавати атрибути та методи на льоту**

```python 
class Demo:
  pass 

obj = Demo()
obj.name = "Динамічний атрибут"
print(obj.name) # "Динамічний атрибут"
```
**Додавання методу на льоту:**

```python 
def new_method(self):
  return "Я новий метод"

Demo.new_method = new_method # Додаємо метод класу 
obj = Demo()
print(obj.new_method()) # "Я новий метод!"
```
**Динамічна гнучкість Python дозволяє модифікувати класи у процесі виконання**

***Висновки:***

| Функціональність | Як працює |
| -------------- | --------------- |
| **Методи класу (`@classmethod`)** | Працюють із класом, отримують `cls` |
| **Статичні методи (`@staticmethod`)** | Функції всередині класу без доступу до `self` |
| **Дескриптори (`property`)** | Контролюють доступ до атрибутів |
| **Поліморфізм (`duck typing`)** | Перевіряє лише наявність методів, а не тип |
| **Абстрактні класи (`ABC`)** | Визначають контракт для дочірніх класів |
| **Динамічне додавання** | Можна змінювати клас під час виконання |


