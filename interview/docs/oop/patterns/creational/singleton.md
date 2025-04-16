# Singleton (Одинак) в Python 

**Singleton (Одинка)** - це породжувальний патерн, який **гарантує, що у класу є тільки один екземпляр, і надає до нього глобальну точку доступу**.

**Навіщо потрібен Singleton?**
- **Контроль доступу** до ресурсу (наприклад, підключення до бази даних).
- **Глобальний стан** для всіх частин програми.
- **Оптимізація пам'яті** - уникаємо створення зайвих екземплярів.

1. ***Реалізація через статичний атрибут класу***

```python 
class Singleton:
  _instance = None # Зберігаємо єдиний екземпляр

  def __new__(cls, *args, **kwargs):
    if cls._instance is None:
      cls._instance = super().__new__(cls) # Створюємо об'єкт лише один раз 
    return cls._instance 

# Тестуємо
obj1 = Singleton()
obj2 = Singleton()

print(obj1 is obj2) # True (це той самий об'єкт)
```

**Як це працює?**
- При першому виклику `Singleton()` створюється новий екземпляр.
- При наступних викликах повертається вже створенний об'єкт.

2. ***Реалізація через декоратор (зручно для швидкого використання)***

```python 
def singleton():
  instances = {}

  def get_instance(*args, **kwargs):
    if cls not in instances:
      instances[cls] = cls(*args, **kwargs)
    return instances[cls]
  
  return get_instance

@singleton
class Logger:
  def log(self, message):
    print(f"[LOG]: {message}")
    
# Тестуємо 
logger1 = Logger()
logger2 = Logger()

print(logger1 is logger2) # True 
logger1.log("Hello Singleton!") # [LOG]: Hello Singleton!
```

**Декоратор `@singleton` забезпечеє, що `Logger` бути мати тільки один екземпляр**

3. ***Реалізація через метаклас (найпотужніший варіант)***

```python 
class SingletonMeta(type):
  instances = {}

  def __cal__(cls, *args, **kwargs):
    if cls not in cls._instances:
      cls._instances[cls] = super().__call__(*args, **kwargs)
      
    return cls._instances[cls]


class DatabaseConnection(metaclass=SingletonMeta):
  def connect(self):
    return "Connecting to database..."

# Тестуємо
db1 = DatabaseConnection()
db2 = DatabaseConnection()

print(db1 is db2) # True 
print(db1.connect()) # Connecting to database...
```

**Як це працює?**
- `SingletonMeta` змінює поведінку створення об'єкта (`__call__`), щою гарантувати, що буде тільки один екземпляр класу.

***Де використовується Singleton у реальному коді?***
- **Логування (Logger)** - збереження глобального стану.
- **Кешування даних** - щоб уникнути повторних запитів до БД.
- **Підключення до бази даних** - щоб не створювати нові з'єднання щоразу.
- **Конфігурації (Config Manager)** - централізоване зберігання налаштувань.

***Недоліки Singleton***
- **Порушує принцип єдиного обов'язку (SRP)** - змішування логіки створення об'єкта з його поведінки.
- **Погано тестується** - ускладнює модульне тестування через глобальний стан.
- **Може викликати труднощі у багатопоточному середовищі** (потрібна синхронізація).
