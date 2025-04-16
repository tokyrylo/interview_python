# ООП
***Об'єктно орієнтоване прогрумування*** - це підхід до програмування, в якому програма організується у вигляді об'єктів. **Об'екти** є екземплярами **класів** та містять дані(атрибути) і поведінку(методи)

## Основні поняття ООП

 - ***Клас(Class)*** - це шаблон, який створює об'єкти. Він містить атрибути та методи

 ```python
class Car:
  def __init__(self, brand, model):
    self.brand = brand # Атрибут класу 
    self.model = model # Атрибут класу 

  def drive(self): # Метод класів
    print(f"{self.brand} {self.model} drive!")
```

 - ***Об'єкт(Object)*** - це екземпляр класу, який має свою унікальну копію атрибути
 ```python
  car1 = Car("Testla", "Model S")
  car2 = Car("BMW", "x5")

  car1.drive() #  Testla Model S drive!
  car2.drive() # BMW x5 drive!
  ```


## Основні чотири принципи ООП

1. ***Інкапсуляція*** - обмежує прямий доступ до даних об'єкта, приховуючи їх всередині класу.
```python
class BankAccount:
  def __init__(self, balance):
    self.__balance = balance # Приватний атрибут (не можна змінювати)

  def deposit(self, amount):
    self.__balance += amount

  def get_balance(self):
    return self.__balance 

account = BankAccount(10000)
account.deposit(500)
print(account.get_balance()) # 10500
```

2. ***Наслідування*** - дозволяє створювати нові класи на основі існуючих.
```python
class ElectricCar(Car):
  def __init__(self, brand, model, baterry_capacity):
    super().__init__(brand, model) # Використовує конструктор батьківського класу
    self.baterry_capacity = baterry_capacity

  def charge(self):
    print(f"{self.brand} {self.model} charging ({self.baterry_capacity} kW)")

tesla = ElectricCar("Tesla", "Model 3", 75)
tesla.drive() # Tesla Model 3 drive!
tesla.charge() # Tesla Model 3 charging (75 kW)
```

3. ***Поліморфізм*** - один і той самий метод може працювати по різному в різних класах.
```python
class Animal:
  def speak(self):
    pass  

class Dog(Animal):
  def speak(self):
    return "Gav!"

class Cat(Animal):
  def speak(self):
    return "Meow!"

animals = [Dog(), Cat()]

for animal in animals:
  print(animal.speak()) # Gav!, Meow!
```

4. ***Абстракція*** - приховує складні деталі реалізації та залишає лише необхідний функціонал

```python
from abc import ABC, abstractmethod


class Anima(ABC):
  @abstractmethod
  def make_sound(self):
    pass


class Dog(Animal):
  def make_sound(self):
    return "Bark!"

dog = Dog()
print(dog.make_sound()) # Bark!
```
