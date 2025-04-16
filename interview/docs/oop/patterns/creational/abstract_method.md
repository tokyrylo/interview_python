# Abstract Factory (Aбстрактна фабрика) в Python.

**Абстрактна фабрика** - це породжувальний патерн, який дозволяє створювати сімейства взаємопов'язаних об'єктів без прив'язки до конкретних класів.

 - **Головна відмінність від Factory Method - Abstract Factory** повертає не один об'єкт, а **групу об'єктів, що належить до однієї логічної категорії**.

***Основні особливості патерну:***

  - **Забезпечює узгодженність об'єктів** - усі об'єкти, створенні конкретною фабрикою.
  - **Інкапсулює створення об'єктів** - клієнтський код не залежить від конкретних класів.
  - **Легко розширюється** - можна додати нову конкретну фабрику без зміни існуючого коду.

***Приклад: UI-бібліотека (Windows/Mac)***:

**Припустимо, у нас є графічний інтерфейс, який має працювати і в Windows, і в MacOS**

Кнопки (`Button`) та чекбокси (`Checkbox`) повинні мати свої унікальні стилі для кожної ОС.

1. **Оголошуємо загальні інтерфейси для продуктів (кнопок і чекбоксів)**

```python 
from abc import ABC, abstractmethod 

# Абстрактні класи для елементів UI 
class Button(ABC):
  @abstractmethod
  def render(self):
    pass 


class Checkbox(self):
  @abstractmethod
  def render(self):
    pass
```

2. **Створюємо конкретні реалізації для Windows та Mac**

```python 
# Реалізація для Windows
class WindowsButton(Button):
  def render(self):
    return "Windows Button"

class WindowsCheckbox(Checkbox):
  def render(self):
    return "Windows Checkbox"

# Реалізація для Mac
class MacButton(Button):
  def render(self):
    return "Mac Button"


class MacCheckbox(Checkbox):
  def render(self):
    return "MacCheckbox"
```

3. **Абстрактна фабрика для створення елементів UI**

```python 
class UIFactory(ABC):
  @abstractmethod
  def create_button(self) -> Button:
    pass 

  @abstractmethod
  def create_checkbox(self) -> Checkbox:
    pass
```

4. **Конкретні фабрики для кожної ОС**

```python 
class WindowsUIFactory(UIFactory):
  def create_button(self) -> Button:
    return WindowsButton()

  def create_checkbox(self) -> Checkbox:
    return WindowsCheckbox()


class MacUIFactory(UIFactory):
  def create_button(self) -> Button:
    return MacButton()

  def create_checkbox(self) -> Checkbox:
    return MacCheckbox()
```

5. **Використання Abstract Factory**

```python 
def get_ui_factory(os_type: str) -> UIFactory:
  if os_type == "Windows":
    return WindowsUIFactory()
  elif os_type == "Mac":
    return MacUIFactory()
  else:
    raise ValueError("Unsupported OS")

# Отримуємо фабрику для потрібної ОС 
factory = get_ui_factory("Mac")

# Створюємо UI-елементи через фабрику 
button = factory.create_button()
checkbox = factory.create_checkbox()

print(button.render()) # "Mac Button"
print(checkbox.render()) # "Mac Checkbox"
```

***Де використовується Abstract Factory?***
  - **Django ORM** - `Database backends (PostgreSQL, MySQL, SQLite)`.
  - **logger.getLogger()** - повертає логери різних видів.
  - **Tkinter/PyQt** - створюють UI-елементи відповідно до ОС.

***Коли варто використовувати?***

  - Коли потрібно створювати **групи об'єктів, що мають спільну логіку**.
  - Коли важливо, щоб **об'єкти були сумістними** (наприклад, UI-компоненти для Windows та Mac).
  - Коли код має **бути розширюваним**, досволяючи легко додавати нові сімейства об'єктів.
