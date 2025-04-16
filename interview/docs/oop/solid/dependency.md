# Dependency Inversion Principle

***Принцип інверсії залежності*** - залежності повинні бути на основі абстракцій, а не на конкретних реалізаціях.

**Що це означає?**

1. **Високорівневі модулі** не повинні залежи від низькорівневих. **Обидва повинні залежати від абстракцій**.
2. **Абстракції не повинні залежати від деталей**. Деталі мають залежати від абстракцій.

**Проблема без DIP** - якщо високорівневий клас напряму використовуює конкретний клас, при зміні останього доведеться змінювати і перший.

**Приклад порушення принципу DIP**:
```python
class MySQLDatabase:
  def connect(self):
    return "Connected to MySQL"


class DataService:
  def __init__(self):
    self.db = MySQLDatabase()

  def get_data(self):
    return self.db.connect()
```

**Що не так?**

- `DataService` **жорстко прив'язаний до** `MySQLDatabase`.
- Якщо потрібно змінити базу на PostgreSQL - доведеться міняти код всередині `DataService`.
- Немає можливості підставити іншу базу даних **без зміни коду**.

**Виправлення за DIP**

```python
from abc import ABC, abstractmethod


class Database(ABC):
  @abstractmethod
  def connect(self):
    pass 


class MySqlDatabase(Database):
  def connect(self):
    return "Connect to MySQL"


class PostgreSQLDatabase(Database):
  def connect(self):
    return "Connected to PostgreSQL"


class DatabaseService:
  def __init__(self, db: Database):
    self.db = db 

  def get_data(self):
    return self.db.connect()

# Використання 
mysql_service = DatabaseService(MySqlDatabase())
postgres_service = DataService(PostgreSQLDatabase())

print(mysql_service.get_data()) # Connected to MySQL
print(postgres_service.get_data()) # Connected to PostgreSQLDatabase
```

**Що змінилося?**
- `DataService` працює з **абстракцією** `Database`, а не з конкретною реалізацією.
- Тепер можна легко змінювати **MySQL -> PostgreSQL без змін у** `DataService`
- Це спрощює **тестування** - можна створити **фейкову базу** для тестів.

**Висновок DIP допомогає**
