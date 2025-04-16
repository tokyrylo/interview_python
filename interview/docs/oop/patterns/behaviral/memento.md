# Знімок (Memento)

## Суть патерну

**Memento (Знімок)** - це поведінковий патерн, який дозволяє зберігати і відновлювати **попередній сан об'єкта** без порушення його інкапсуляції.
Ідеально підходить для реалізації функціоналу **Undo/Redo**, або збереження "контрольних точок".

## Проблема

Уявимо текстовий редактор, де користувач хоче мати можливість повернутися до попередньої версії тексту. Але ми не хочемо відкривати всі поля об'єкта редактора, порушуючи інкапсуляцію.

## Рішення

Патерн `Memento` створює окремий об'єкт-знімок, який зберігає стан іншого об'єкта. Сам об'єкт-одержувач (originator) може створити цей знімок і відновити себе з нього.

## Приклад

```python
# Знімок (стан)
class Memento:
    def __init__(self, text: str):
        self._text = text

    def get_saved_state(self) -> str:
        return self._text

# Одержувач (редактор)
class TextEditor:
    def __init__(self):
        self._text = ""

    def type(self, words: str):
        self._text += words

    def get_text(self):
        return self._text

    def save(self) -> Memento:
        return Memento(self._text)

    def restore(self, memento: Memento):
        self._text = memento.get_saved_state()

# Опікун (керує знімками)
class History:
    def __init__(self):
        self._history = []

    def backup(self, memento: Memento):
        self._history.append(memento)

    def undo(self) -> Memento:
        if not self._history:
            return None
        return self._history.pop()

# Використання
editor = TextEditor()
history = History()

editor.type("Привіт, ")
history.backup(editor.save())

editor.type("світ!")
history.backup(editor.save())

editor.type(" Я люблю Python 🐍")

print("📄 Поточний текст:", editor.get_text())  # Привіт, світ! Я люблю Python

# Undo
editor.restore(history.undo())
print("↩️ Undo 1:", editor.get_text())  # Привіт, світ!

editor.restore(history.undo())
print("↩️ Undo 2:", editor.get_text())  # Привіт,
```

## Коли використовувати

 - Коли, потрібно реалізувати **відкат змін**(Undo/Redo).
 - Коли важливо зберегти інкапсуляцію - не відкривати всі поля об'єкту.
 - Коли потрібно зберігати **контрольні точки стану**.

## Переваги
 - Не порушує інкапсуляцію
 - Просте збреження і відновлення стану.
 - Легко реалізувати історію змін.

## Недоліки
 - Якщо стан об'єкта великий - знімки можуть займати багато пам'яті.
 - Управління великою кількістю знімків може бути складним.
 - Не завжди очевидно, які частини стану зберігати (потрібно планувати).

## Аналогія з життя

Якщо ти працюєш над документами у Word або Google Docs, кожне збереження - це **знімок**. Функція "**Undo**" - це по суті відновлення попереднього знімку.

## Типові сценарії
 - Текстові редактори.
 - Ігри (збереження рівнів).
 - Фінансові программи (повернення до попередніх станів рахунків).
 - Об'єкти з часто змінюваним станом.
