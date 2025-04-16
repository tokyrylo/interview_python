# Single Responsibility 

Один з п'яти принципів **об'єктно орієнтованого програмування ООП**, які допомогають створювати підтримуваний, розгирюваний та гнучкий код.

***Single Responsibility Principle*** - принцип єдиної відповідальності, кожен клас повинен мати лише одну причину для зміни.

- **Суть**: Кожен клас(або модуль) повинен відповідати **тільки за одну конкретну задачу**.
- **Чому важливо**: Якщо клас виконує кілька задач, то зміни в одній частині можуть неочікувано зламати іншу.

***Приклад***:

```python
class Report:
  def __init__(self, data):
    self.data = data

  def generate_report(self):
    return f"Report Data: {self.data}"

  def save_to_file(self, filename):
    with open(filename, "w") as f:
      f.write(self.generate_report())

  def send_email(self, email):
    print(f"Send report to {email}")
```
Тут клас **Report** виконує **три завдання**:

1. Генерація звіту(`generate_report`)
2. Збереження у файл(`save_to_file`)
3. Відправка email(`send_email`)

Якщо зміниться логіка збереження у файл або формування звіту, нам доведеться змінювати цей самий клас.

***Як виправити?***:

```python
class Report:
  def __init__(self, data):
    self.data = data

  def generate_report(self):
    return f"Report data: {self.data}"


class FileSaver:
  @staticmethod
  def save_to_file(report: Report, filename):
    with open(filename, "w") as f:
      f.write(report.generate_report())

class EmailSender:
  @staticmethod
  def send_email(report: Report, email):
    print(f"Send report to {email}")
```

**Тепер**:
- **Report** генерує звіт.
- **FileSaver** зберігає.
- **EmailSender** відправляє.

Кожен клас відповідає **тільки за одну задачу**, тому зміни в одному місці **не впливають на інші частини коду**

***Висновок***:
**Принцип єдиної відповідальності(SRP)** допомогає:

- зробити код **простішим для підтримки**
- **зменшити залежності** між частинами системи
- **легше тестувати** та змінювати код без побічних ефектів


