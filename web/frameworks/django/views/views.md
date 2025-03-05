# Django Views 

У Django є два основних підходи до створення представлень (views):

 - **FBV (Function-Based Views)** - на основі функцій
 - **CBV (Class-Based Views)** - на основі класів

Обидва підходи використовуються для обробки HTTP-запитів, але кожен має свої особливості.

***1. Function-Based Views (FBV)***
**FBV** - це звичайні функції Python, які приймають запит (`request`) і повертають відповідь (`HttpResponse`).

**Приклад FBV**

```python 
from django.http import HttpResponse


def hello_view(request):
  return HttpResponse("Hello, world!")
```

Тут `hello_view` - це звичайна функція, яка приймає HTTP-запит і повертає відповідь.

***2. Class-Based Views (CBV)***

**CBV** - використовує класи для створення представлень. Django надає готові базові класи (`View`, `TemplateView`, `ListView`, тощо), які спрощують розробку.

**Приклад CBV**

```python
from django.http import HttpResponse
from django.views import View 


class HelloView(View):
  def get(self, request):
    return HttpResponse("Hello, world!")
```

Тут `HelloView` - це клас, який успадковується від `View` і оброблює `GET`-запити.

***Порівняння FBV та CBV***


|  | FBV (Function-Based View) | CBV (Class-Based View) |
| --------------- | --------------- | --------------- |
| **Структура** | Використовує функцію | Використовує клас |
| **Легкість написання** | Прості у використанні | Можуть бути складнішими |
| **Гнучкість** | Легко кастомізувати | Використовує наслідування |
| **Повторне використання коду** | Часто потребує дублювання коду | Дозволяє уникати дублювання через наслудвання |
| **Обробка методів** | Все в одній функції (`if request.method == "POST"`) | Окремі методи (`get()`, `post()`, `put()`, `delete()`) |
| **Читабельність** | Простий код, добре читається | Код може бути важчим для розуміння через наслідування |


***4. Приклад CRUD-операцій у FBV та CBV***

**FBV (Function-Based View)**

```python 
from django.shortcuts import render, get_objects_or_404, redirect

from .models import Post 


def post_list(request):
  posts = Post.objects.all()
  return render(request, "post_list.html", {"posts": posts})

def post_detail(request, pk):
  post = get_objects_or_404(Post, pk=pk)
  return render(request, "post_detail.html", {"post": post})

def post_create(request):
  if request.method == "POST":
    title = request.POST.get("title")
    Post.objects.create(title=title)
    return redirect("post_list")
  return render(request, "post_form.html")


def post_delete(request, pk):
  post = get_objects_or_404(Post, pk=pk)
  post.delete()
  return redirect("post_list")
```

**CBV (Class-Based Views)**

```python
from django.views.generic import ListView, DetailView, CreateView, DeleteView 
from django.urls import reverse_lazy


class PostListView(ListView):
  model = Post
  template_name = "post_list.html"
  context_object_name = "posts"


class PostDetailView(DetailView):
  model = Post 
  template_name = "post_detail.html"


class PostCreateView(CreateView):
  model = Post 
  fields = ["title"]
  template_name = "post_form.html"
  success_url = reverse_lazy("post_list")


class PostDeleteView(DeleteView):
  model = Post
  success_url = reverse_lazy("post_list")
```
CBV спрощує реалізацію стандартних CRUD-операцій через Django Generic Views.

***5. Коли використовувати FBV, а коли CBV?***

| Випадок | FBV | CBV |
| --------------- | --------------- | --------------- |
| **Простий код** (наприклад, повернення `HttpResponse`) | [v] | [x] |
| **CRUD-операції** | [x] | [v] |
| **Обробка різних HTTP-методів в одному місці** | [v] | [x] |
| **Наслідування і розширення логіки** | [x] | [v] |
| **Багаторазове використання коду** | [x] | [v] |
| **Додання міксінів** (наприклад, `LoginRequiredMixin`) | [x] | [v] |

***Висновок***

 - **FBV** - підходить для простих представлень і логіки, що не потребує складних взаємодій.
 - **CBV** - краще підходить для маштабованих проектів, повторного використання коду та стандартних операцій (CRUD).
 - Обидва підходи можна комбінувати залeжно від потреб вашого проекту.
