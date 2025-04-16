# Open/Closed Principle

***Принцип відкритості/закритості*** - код повинен бути відкритий для розширення, але закритий для модіфікації.

**Що це означає?**

- Ти **можеш додавати новий функціонал** без зміни існуючого коду.
- Але **не повинен змінювати старий код** щоразу, коли з'являється нова вимога.

Це  особливо важливо для великих проектів, де зміни в існуючому коді можуть викликати непередбачені помилки.

**Приклад порушення OCP**

```python
class Employee:
  def __init__(self, name, role):
    self.name = name
    self.role = role

  def calculate_salary(self):
    if self.role == "developer":
      return 5000
    if self.role == "manager":
      return 7500
    else:
      return 4000

```
**Проблема**:
- Якщо додати нову роль (наприклад, "директор"), доведеться змінювати метод `calculate_salary`, а це порушує принцип **OCP**.
- Кожна зміна підвищує ризик зламати код.

**Як виправвити?**

Треба використовувати **поліморфізм** - кожен тип працівника матиме свій окремий клас.
```python
from abc import ABC, abstractmethod


class Employee(ABC):
  def __init__(self, name):
    self.name = name

  @abstractmethod
  def calculate_salary(self):
    pass 


class Developer(Employee):
  def calculate_salary(self):
    return 5000


class Manager(Employee):
  def calculate_salary(self):
    return 7500


class Director(Employee):
  def calculate_salary(self):
    return 10000


# Використання 

employees = [
  Developer("Alice"),
  Manager("Bob"),
  Director("Charlie"),
]

for empl in employees:
  print(f"{empl.name}: ${empl.calculate_salary()}")
```
**Що змінилось**

- Якщо з'явиться нова роль, достатньо **додати новий клас** `Analyst`, не змінюючи старий клас.
- Код став **маштабованим та стабільним**

**Висновок**:

**OCP допомогоє**:

1. Робити систему **гнучкою**
2. **Мінімалюзувати ризик** поломок при додаванні нового функціоналу.
3. **Уникати зайвих перевірок `if/else`**, замінюючи їх **поліформізмом**


