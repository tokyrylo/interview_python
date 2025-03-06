# Django forms 

**Форми** в Django використовуються для обробки введених користувачем даних. Django надає вбудовону систему форм, яка дозволяє легко створювати, перевіряти та обробляти дані в запитах (наприклад, у формах HTML).
Основні властивості Django Forms:

 - Генерація HTML-форм автоматично.
 - Вбудована валідація (перевірка коректності даних).
 - Захист від CSRF-атак.
 - Легка інтеграція з моделями бази даних.

***Основний клас Django Forms***

Всі форми в Django успадковуються від `django.forms.Form` або `django.forms.ModelForm`.
Ось приклад просту форму `Form`:

```python 
from django import forms 


class ContactForm(forms.Form):
  name = forms.CharField(max_length=100)
  email = forms.EmailField()
  message = forms.CharField(widget=forms.Textarea)

# Використання у views.py 

from django.shortcuts import render 

from .forms import ContactForm


def contact_view(request):
  if request.method == "POST":
    form = ContactForm(request.POST)
    if form.is_valid():
      # Обробка даних (збереження, відправка email тощо)
      print(form.cleaned_data)
  else:
    form = ContactForm()

  return render(request, "contact.html", {"form": form})
```
У цьому прикладі `form.cleaned_data` містить перевірені дані, які користувач ввів у форму.



