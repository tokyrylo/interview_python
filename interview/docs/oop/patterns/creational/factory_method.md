# Factory Method (Фабрічний метод) в Python 

**Factory method** - це породжувальний патерн, який дозволяє створювати об'єкти, не вказуючи їх конкретний клас, а делегуючи створення підкласам. Це забезпечює гнучкість і розширюваність коду, оскільки можна додавати нові типи об'єктів без зміни існуючого коду.

**Ключові особливості патерну:**
  - Визначає загальний інтерфейс для створення об'єктів.
  - Підкласи самі вирішують, який конкретний клас створити.
  - Використовується, коли зазделегіть невідомо, який об'єкт потрібно створювати.
  - Дає можливість динамічно змінювати клас створення об'єктів.

**Приклад реалізації Factory Method в Python**:

1. **Без патерну (статичне створення об'єктів)**

```python 
class Dog:
  def speak(self):
    return "Woof!"

class Cat:
  def speak(self):
    return "Meow!"

def get_pet(pet_type):
  if pet_type == "dog":
    return Dog()
  elif pet_type == "cat":
    return Cat()
  return None 

pet = get_pet("dog")
print(pet.speak) # "Woof!"
```
**Недоліки:**
- Якщо потрібно додати новий тип тварин (наприклад, `Parrot`), доведеться змінювати `get_pet()`, що порушує принцип **Open/Closed (OCP)**

```python 
from abc import ABC, abstractmethod

# Абстрактний клас (інтерфейс) для тварин 
class Pet(ABC):
  @abstractmethod
  def speak(self):
    pass

# Конкретні реалізації тварин 
class Dog(Pet):
  def speak(self):
    return "Woof!"

class Cat(Pet):
  def speak(self):
    return "Meow!"

# Абстрактний клас-фабрика
class PetFactory(ABC):
  @abstractmethod
  def create_pet(self):
    pass 

# Конкретна фабрика 
class DogFactory(PetFactory):
  def create_pet(self):
    return Dog()

class CatFactory(PetFactory):
  def create_pet(self):
    return Cat()

# Використання фабричних методів
dog_factory = DogFactory()
dog = dog_factory.create_pet()
print(dog.speak()) # "Woof!"

CatFactory = CatFactory()
cat = cat_factory.create_pet()
print(cat.speak()) # "Meow!"
```

**Переваги:**

 - Можна  легко додати нові типи об'єктів, не змінюючи базовий код.
 - Код більш структурований та відповідає SOLID-принципам.
 - Відокремлення логіки створення від клієнського коду.

***Де використовується Factory Method***
 - **Django ORM** - `Model.objects.create()`
 - **logging.getLogger(name)** - повертає різні логери.
 - **multiprocessing.Process()** - створює новий процес.

**Коли варто використовувати Factory Method?**
 - Коли необхідно приховати деталі створення об'єкта.
 - Коли потрібно створювати об'єкти динамічно на основі вхідних даних.
 - Коли важливо, щоб об'єкти можна було легко замінювати новими варіантами.


