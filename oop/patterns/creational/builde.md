# Builder (Будівник) у Python 

**Builder (Будівник)** - це породжувальний патерн, який дозволяє **покроково створювати складні об'єкти**, розділяючи процес побудови від самої структури об'єкта.

**Навіщо потрібен Builder?**
  - Уникнення **довгих конструкторів** з багатьма аргументами.
  - Гнучкість при створенні складних об'єктів.
  - Код легше **читати та розширювати**.
  - Створення об'єкта **можна розбити на кілька кроків**.

***Класичний приклад: Створення автомобіля***

Уявимо, що нам потрібно створювати об'єкти автомобілів (`Car`) із різними характеристиками (двигун, колеса, GPS тощо).

1. **Клас, що будується (Car)**
```python 
class Car:
  def __init__(self):
    self.engine = None 
    self.wheels = None 
    self.gps = None 

  def __str__(self):
    return f"Car(engine={self.engine}, wheels={self.wheels}, gps={self.gps})"
```

2. **Інтерфейс будівника**

```python 
from abc import ABC, abstractmethod 


class CarBuilder(ABC):
  @abstractmethod
  def set_engine(self, engine_type):
    pass 

  @abstractmethod
  def set_wheels(self, wheel_count):
    pass 

  @abstractmethod
  def set_gps(self, has_gps):
    pass 

  @abstractmethod
  def get_result(self) -> Car:
    pass 
```

3. **Конкретний будівник для автомабіля**

```python 
class ConcreteCarBuilder(CarBuilder):
  def __init__(self):
    self.car = Car()

  def set_engine(self, engine_type):
    self.car.engine = engine_type
    return self 

  def set_wheels(self, wheel_count):
    self.car.wheels = wheel_count
    return self 

  def set_gps(self, has_gps):
    self.car.gps = has_gps
    return self 

  def get_result(self) -> Car:
    return self.car 
```

4. **Director (Необов'язково)**

Director - це клас, який визначає, **які кроки потрібно виконати** для створення конкретного об'єкта.

```python 
class CarDirector:
  def __init__(self, builder: CarBuilder):
    self.builder = builder 

  def construct_sports_car(self):
    return self.builder.set_engine("V8").set_wheels(4).set_gps(True).get_result()

  def construct_economy_car(self):
    return self.builder.set_engine("v4").set_wheels(4).set_gps(False).get_result()
```

5. **Використання (Builder)**

```python 
# Створюємо будівника
builder = ConcreteCarBuilder()

# Будуємо кастомний автомобіль 
custom_car = builder.set_engine("V6").set_wheels(4).set_gps(True).get_result()
print(custom_car) # Car(engine=V6, wheels=4, gps=True)

# Викоритовуємо Director для створення стандартних машин
director = CarDirector(builder)

sports_car = director.construct_sports_car()
print(sports_car) # Car(engine=V8, wheels=4, gps=True)

economy_car = director.construct_economy_car()
print(economy_car) # Car(engine=V6, wheels=4, gps=False)
```

***Де використовується Builder у реальному коді?***
  - `logging.Logger` - створює об'єкти логера з різними конфігураціями.
  - `Django QuerySet` - використовує **ланцюжкові виклики** (`.filter().order_by()`).
  - `SQLAlchemy ORM` - створення запитів до бази даних через `session.query()`.

***Коли варто використовувати Builder?***
- Якщо клас має **багато параметрів**, і конструктор стає занадто складними.
- Якщо об'єкт **потрібно створювати покроково** (наприклад, формувати складний документ).
- Якщо є **різні варіанти конфігурацій** об'єкта (наприклад, спорткар чи економ-авто).
