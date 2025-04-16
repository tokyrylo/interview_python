# Команда (Command)

## Суть патерна

**Команда** - це поведінковий патерн, який перетворює запити або прості операції на об'єкти. Це дозволяє передавати їх як параметри, зберігати в чергах, логувати, або відкладати виконання.

## Проблема

Уявимо собі пульт дістанційного керування: натискання кнопки - це запит на дію. Але замість того, щоб кнопка сама знала, що робити, вона лише передає команду виконавцю. Так само і в коді - часто потрібно відділити ініціатора дії від того, хто її виконує.

## Рішення

Описати всі дії як окремі **команди** - об'єкти з єдиним методом `execute()`. Кожна команда знає, як викликати необхідний метод у отримувача.

## Приклад реалізації

```python
from abc import ABC, abstractmethod

# Інтерфейс команди
class Command(ABC):
    @abstractmethod
    def execute(self):
        pass

# Отримувач (реальний виконавиць дій)
class Light:
    def turn_on(self):
        print("Світло увімкнино")

    def turn_off(self):
        print("Світло вимкнене")

# Конкретні команди
class TurnOnCommand(Command):
    def __init__(self, light):
        self.light = light

    def execute(self):
        self.light.turn_on()

class TurnOffCommand(Command):
    def __init__(self, light):
        self.light = light

    def execute(self):
        self.light.turn_off()

# Ініцітор (інворкер)
class RemoteControl:
    def __init__(self):
        self._commands = {}

    def set_command(self, name: str, command: Command):
        self._commands[name] = command

    def press_button(self, name: str):
        if name in self._commands:
            self._commands[name].execute()
        else:
            print(f"Кнопка {name} не налаштована.")

# Використання
light = Light()
on_command = TurnOnCommand(light)
off_command = TurnOffCommand(light)

remote = RemoteControl()
remote.set_command("on", on_command)
remote.set_command("off", off_command)

remote.press_button("on") # Світло увімкнене
remote.press_button("off") # Світло вимкнене
```

## Коли використовувати
 - Коли потрібно передавати дії як об'єкти (наприклад, для реалізації **історії дії** або **undo/redo**).
 - Коли важливо розділити відповідальність: **хто ініціює дію** /= **хто її виконує**.
 - Коли потрібно логувати, зберігати або ставити запити в чергу.

## Переваги
 - Інкапсуляція логики виконання дії.
 - Просте додання нових команд без зміни існуючого коду.
 - Зручно реалізовувати історію, черги, undo/redo.

## Недоліки
 - Може з'явитися багато маленьких класів-команд.
 - Ускладнює структуру коду для простих задач.

## Аналогія з життя
Коли ти натискаєш кнопку на пульті (наприклад, вмикання світла), **ти не  керуєш електрикою напряму**, а лише надсилаєш "команду", які інший пристрій виконує.
