# Django Project vs Django App – Difference

## ✅ Django Project Kya Hota Hai?

- Django project **overall container / main setup** hota hai.
- Isme poori website ya system ka configuration hota hai.
- Project ke andar multiple apps ho sakti hain.
- Ye high-level structure hota hai.

**Isme usually hota hai:**
- settings.py → project settings
- urls.py → main URL routing
- wsgi.py / asgi.py → deployment related
- manage.py → project manage karne ka tool

👉 Ek project = poori website / system

---

## ✅ Django App Kya Hoti Hai?

- Django app ek **specific feature/module** hoti hai.
- Har app ek kaam handle karti hai.
- Apps reusable hoti hain (dusre projects me bhi use kar sakte ho).

**Example apps:**
- User management app
- Payment app
- Blog app
- Order app

**App ke andar usually hota hai:**
- models.py → database tables
- views.py → business logic
- urls.py → app routes
- admin.py → admin panel config
- forms.py → forms logic

👉 Ek app = ek feature/module

---

## ✅ Simple Example

Agar tu ek E-commerce site bana raha hai:

**Project:** ecommerce_site  
**Apps inside project:**
- users app
- products app
- orders app
- payments app

---

## ✅ Short Interview Answer (One Line)

> Django Project is the main configuration container of the whole website, while a Django App is a reusable module that handles a specific functionality inside the project.

> Django Project is the main configuration container that holds the complete website setup, while a Django App is a reusable module inside the project that implements a specific feature like users, blog, or payments. 


| Point      | Django Project          | Django App                 |
| ---------- | ----------------------- | -------------------------- |
| Meaning    | Whole website container | Feature/module             |
| Scope      | High level              | Specific functionality     |
| Count      | Usually one             | Multiple ho sakti hain     |
| Reusable   | Nahi                    | Haan, dusre project me use |
| Contains   | Global settings         | Models, views, logic       |
| Created By | startproject            | startapp                   |


# Django Template Engine – Kya Hota Hai & Kyu Use Hota Hai?

## ✅ Template Engine Kya Hota Hai?

Template Engine ek system hota hai jo **dynamic HTML pages generate** karta hai.  
Ye backend (Python/Django) se data lekar frontend (HTML) me show karta hai.

Simple words me:

> Template engine = HTML + dynamic data ko combine karne ka tool

Django ka apna built-in template engine hota hai — **Django Template Language (DTL)**.

---

## ✅ Ye Problem Solve Karta Hai

Agar template engine na ho to:

- Har page manually HTML me banana pade
- Dynamic data show karna mushkil
- Logic aur HTML mix ho jata

Template engine:

- Data inject karta hai
- HTML clean rakhta hai
- Logic aur design separate karta hai

---

## ✅ Kaise Kaam Karta Hai (Flow)



User Request → View → Data from Model → Template → Final HTML → Browser


View data bhejta hai template ko → template engine data ko HTML me fill karta hai → page render hota hai.

---

## ✅ Basic Example

### 🔹 View (views.py)

```python
def home(request):
    return render(request, "home.html", {"name": "Bittu"})

### 🔹 Template (home.html)

```html
<h1>Hello {{ name }}</h1>


# ✅ Template Engine Features

### 🔸 Variables Show Karna

{{ user.username }}
{{ product.price }}


### 🔸 Loops

{% for p in products %}
  {{ p.name }}
{% endfor %}


### 🔸 Conditions

{% if user.is_authenticated %}
  Welcome User
{% endif %}


### 🔸 Template Inheritance

{% extends "base.html" %}
```

### ✅ Template Engine Use Kyu Karte Hain?

* Dynamic content show karne ke liye
* Backend data ko HTML me inject karne ke liye
* Code separation (logic vs UI)
* Reusable layouts banane ke liye
* Clean & maintainable code

# Django Model → Field → Save Data → Search/Fetch Data – Full Flow Example

Below is a **complete mini flow** jisme:
- New model create
- Fields define
- Migration
- Data create/save
- Multiple conditions se search/fetch

Example case: **Product Inventory System**

---

## ✅ Step-by-Step Full Code Flow

```python
# ================================
# models.py
# ================================

from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    category = models.CharField(max_length=50)
    price = models.IntegerField()
    stock = models.IntegerField()
    is_active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name


# ================================
# Create Migration + Apply
# (Terminal Commands)
# ================================

"""
python manage.py makemigrations
python manage.py migrate
"""


# ================================
# Data Create / Save (Django Shell or views.py)
# ================================

from shop.models import Product

# Method 1 — Direct create
p1 = Product.objects.create(
    name="iPhone 15",
    category="mobile",
    price=80000,
    stock=10
)

# Method 2 — Object then save
p2 = Product(
    name="Samsung TV",
    category="electronics",
    price=50000,
    stock=5
)
p2.save()


# ================================
# Fetch / Search Data – Different Conditions
# ================================

# 1️⃣ Get All Products
all_products = Product.objects.all()

# 2️⃣ Filter by exact category
mobiles = Product.objects.filter(category="mobile")

# 3️⃣ Price greater than condition
expensive = Product.objects.filter(price__gt=60000)

# 4️⃣ Price range condition
mid_range = Product.objects.filter(price__gte=30000, price__lte=70000)

# 5️⃣ Stock less than (low inventory alert case)
low_stock = Product.objects.filter(stock__lt=6)

# 6️⃣ Name contains keyword (case insensitive search)
search_name = Product.objects.filter(name__icontains="iphone")

# 7️⃣ Multiple conditions together
combo = Product.objects.filter(
    category="mobile",
    price__gt=50000,
    stock__gt=5
)

# 8️⃣ Exclude condition
not_mobiles = Product.objects.exclude(category="mobile")

# 9️⃣ Order by price high → low
sorted_products = Product.objects.order_by("-price")

# 🔟 Get single object
single_product = Product.objects.get(id=1)

# 1️⃣1️⃣ First / Last record
first_item = Product.objects.first()
last_item = Product.objects.last()

# 1️⃣2️⃣ Count records
total = Product.objects.count()

# 1️⃣3️⃣ Only active products
active_items = Product.objects.filter(is_active=True)


# ================================
# Update Existing Data
# ================================

prod = Product.objects.get(id=1)
prod.price = 82000
prod.save()


# ================================
# Delete Data
# ================================

prod = Product.objects.get(id=2)
prod.delete()


# ================================
# Real Case Query Example
# ================================

"""
Case:
Show all active mobiles
price > 40000
stock > 3
sort by latest
"""

result = Product.objects.filter(
    category="mobile",
    is_active=True,
    price__gt=40000,
    stock__gt=3
).order_by("-created_at")
```


#### Interview Samajhne Wala Summary

* Model = database table define karta hai
* makemigrations = change file banata hai
* migrate = DB update karta hai
* create()/save() = data insert
* filter()/get()/exclude() = data fetch/search
* __gt / __lt / __icontains = conditions lagate hain
* order_by() = sorting

# Django – Backend (Model) → Frontend (Template) Data Send – Full Flow Example

## ✅ Use Case: Product Store Page

Hum ek Product model banayenge aur different ways me data template tak bhejenge:

- Single object
- Multiple objects (list/queryset)
- Filtered data
- Aggregated data (count, avg)
- Context dictionary
- Context shortcut (locals)
- Class Based View method

---

## FULL CODE FLOW (Model → View → URL → Template)

```python
# =========================================
# models.py
# =========================================

from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    category = models.CharField(max_length=50)
    price = models.IntegerField()
    stock = models.IntegerField()
    is_active = models.BooleanField(default=True)

    def __str__(self):
        return self.name


# =========================================
# views.py
# =========================================

from django.shortcuts import render
from django.db.models import Avg, Count
from .models import Product


def product_dashboard(request):

    # -----------------------------
    # Method 1 — Send All Data
    # -----------------------------
    all_products = Product.objects.all()

    # -----------------------------
    # Method 2 — Filtered Data
    # -----------------------------
    mobiles = Product.objects.filter(category="mobile")

    # -----------------------------
    # Method 3 — Single Object
    # -----------------------------
    featured = Product.objects.filter(is_active=True).first()

    # -----------------------------
    # Method 4 — Conditional Data
    # -----------------------------
    expensive_products = Product.objects.filter(price__gt=50000)

    # -----------------------------
    # Method 5 — Aggregated Data
    # -----------------------------
    stats = Product.objects.aggregate(
        total_items=Count("id"),
        avg_price=Avg("price")
    )

    # -----------------------------
    # Method 6 — Values Queryset (Dict form)
    # -----------------------------
    name_price_list = Product.objects.values("name", "price")

    # -----------------------------
    # Method 7 — Context Dictionary
    # -----------------------------
    context = {
        "all_products": all_products,
        "mobiles": mobiles,
        "featured": featured,
        "expensive": expensive_products,
        "stats": stats,
        "name_price": name_price_list,
        "page_title": "Product Dashboard"
    }

    return render(request, "products/dashboard.html", context)



# -----------------------------
# Method 8 — Context using locals()
# -----------------------------
def product_dashboard_locals(request):
    all_products = Product.objects.all()
    mobiles = Product.objects.filter(category="mobile")
    return render(request, "products/dashboard.html", locals())

`यहाँ locals() function current scope के सारे local variables को dictionary में convert कर देता है।`
`locals() current function के सभी local variables को dictionary के रूप में return करता है और उसे context की तरह render() में pass किया जा सकता है। हालांकि Django projects में explicit context dictionary use करना बेहतर माना जाता है क्योंकि वह अधिक readable और maintainable होता है।`



# =========================================
# Class Based View Method (Method 9)
# =========================================

from django.views.generic import ListView

class ProductListView(ListView):
    model = Product
    template_name = "products/dashboard.html"
    context_object_name = "all_products"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["mobiles"] = Product.objects.filter(category="mobile")
        context["page_title"] = "CBV Product List"
        return context



# =========================================
# urls.py
# =========================================

from django.urls import path
from .views import product_dashboard, product_dashboard_locals, ProductListView

urlpatterns = [
    path("dashboard/", product_dashboard),
    path("dashboard-locals/", product_dashboard_locals),
    path("dashboard-cbv/", ProductListView.as_view()),
]



# =========================================
# templates/products/dashboard.html
# =========================================

"""
<h1>{{ page_title }}</h1>

<h2>📦 Featured Product</h2>
{{ featured.name }} - {{ featured.price }}

<h2>📋 All Products</h2>
{% for p in all_products %}
  <p>{{ p.name }} — {{ p.price }}</p>
{% endfor %}

<h2>📱 Mobile Products</h2>
{% for m in mobiles %}
  <p>{{ m.name }}</p>
{% endfor %}

<h2>💎 Expensive Products</h2>
{% for e in expensive %}
  <p>{{ e.name }} - {{ e.price }}</p>
{% endfor %}

<h2>📊 Stats</h2>
Total Items: {{ stats.total_items }}
Average Price: {{ stats.avg_price }}

<h2>🧾 Name & Price Dict Data</h2>
{% for item in name_price %}
  <p>{{ item.name }} - {{ item.price }}</p>
{% endfor %}
"""
```

### ✅ Backend → Template Data Send Karne Ke Methods

| Method           | Kaise                               |
| ---------------- | ----------------------------------- |
| Context Dict     | render(request, template, context)  |
| locals()         | render(request, template, locals()) |
| Single Object    | get() / first()                     |
| Queryset         | filter() / all()                    |
| Dict Data        | values()                            |
| Aggregate        | aggregate()                         |
| Class Based View | get_context_data                    |
| ListView Auto    | context_object_name                 |


# Django – Frontend → Backend Data Send – Full Flow Example

## ✅ Use Case: Product Feedback + Order Form System

Hum ek hi example flow me multiple methods cover karenge jisse data
frontend (template) se backend (Django view/model) me send hota hai:

### 🔥 Methods Covered

1. HTML Form (POST)
2. HTML Form (GET)
3. Django Form Class
4. ModelForm (direct model save)
5. AJAX (fetch API – JSON send)
6. URL Parameter send
7. Query Parameter send

Sab ek hi project-style flow me 👇

---

## FULL END-TO-END CODE FLOW

```python
# =====================================
# models.py
# =====================================

from django.db import models

class Feedback(models.Model):
    name = models.CharField(max_length=100)
    message = models.TextField()
    rating = models.IntegerField()

    def __str__(self):
        return self.name


# =====================================
# forms.py  (Method 3 & 4)
# =====================================

from django import forms
from .models import Feedback

# Django Normal Form
class ContactForm(forms.Form):
    name = forms.CharField()
    message = forms.CharField(widget=forms.Textarea)
    rating = forms.IntegerField()

# ModelForm (direct model save)
class FeedbackModelForm(forms.ModelForm):
    class Meta:
        model = Feedback
        fields = "__all__"


# =====================================
# views.py
# =====================================

from django.shortcuts import render, redirect
from django.http import JsonResponse
from .models import Feedback
from .forms import ContactForm, FeedbackModelForm


def demo_forms(request):

    # -----------------------------
    # Method 1 — POST Form (manual)
    # -----------------------------
    if request.method == "POST" and request.POST.get("form_type") == "basic":
        name = request.POST.get("name")
        message = request.POST.get("message")
        rating = request.POST.get("rating")

        Feedback.objects.create(
            name=name,
            message=message,
            rating=rating
        )

    # -----------------------------
    # Method 2 — GET Form
    # -----------------------------
    if request.GET.get("search_name"):
        search = request.GET.get("search_name")
        results = Feedback.objects.filter(name__icontains=search)
    else:
        results = None


    # -----------------------------
    # Method 3 — Django Form
    # -----------------------------
    form = ContactForm(request.POST or None)
    if form.is_valid():
        Feedback.objects.create(**form.cleaned_data)


    # -----------------------------
    # Method 4 — ModelForm
    # -----------------------------
    mform = FeedbackModelForm(request.POST or None)
    if mform.is_valid():
        mform.save()


    return render(request, "demo.html", {
        "form": form,
        "mform": mform,
        "results": results
    })


# -----------------------------
# Method 5 — AJAX JSON Receive
# -----------------------------
import json

def ajax_submit(request):
    if request.method == "POST":
        data = json.loads(request.body)

        Feedback.objects.create(
            name=data["name"],
            message=data["message"],
            rating=data["rating"]
        )

        return JsonResponse({"status": "saved"})


# -----------------------------
# Method 6 — URL Parameter
# -----------------------------
def rating_by_url(request, rating):
    data = Feedback.objects.filter(rating=rating)
    return JsonResponse({"count": data.count()})


# =====================================
# urls.py
# =====================================

from django.urls import path
from .views import demo_forms, ajax_submit, rating_by_url

urlpatterns = [
    path("demo/", demo_forms),
    path("ajax-submit/", ajax_submit),
    path("rating/<int:rating>/", rating_by_url),
]


# =====================================
# template: demo.html
# =====================================

"""
<h2>Method 1 — POST Form</h2>

<form method="post">
  {% csrf_token %}
  <input name="form_type" value="basic" hidden>
  <input name="name" placeholder="Name">
  <textarea name="message"></textarea>
  <input name="rating">
  <button type="submit">Send POST</button>
</form>


<h2>Method 2 — GET Form</h2>

<form method="get">
  <input name="search_name" placeholder="Search name">
  <button type="submit">Search</button>
</form>


<h2>Method 3 — Django Form</h2>

<form method="post">
  {% csrf_token %}
  {{ form.as_p }}
  <button>Submit Django Form</button>
</form>


<h2>Method 4 — ModelForm</h2>

<form method="post">
  {% csrf_token %}
  {{ mform.as_p }}
  <button>Save ModelForm</button>
</form>


<h2>Method 5 — AJAX Send</h2>

<button onclick="sendAjax()">Send AJAX</button>

<script>
function sendAjax(){
  fetch("/ajax-submit/", {
    method: "POST",
    headers: {"Content-Type": "application/json"},
    body: JSON.stringify({
      name: "Bittu",
      message: "Great site",
      rating: 5
    })
  })
}
</script>


<h2>Method 7 — Query Result Show</h2>

{% if results %}
  {% for r in results %}
    <p>{{ r.name }} - {{ r.rating }}</p>
  {% endfor %}
{% endif %}
"""
```

### ✅ Frontend → Backend Data Send Methods Summary

| Method      | Use Case            |
| ----------- | ------------------- |
| POST Form   | Create/Save data    |
| GET Form    | Search/Filter       |
| Django Form | Validation needed   |
| ModelForm   | Direct model save   |
| AJAX        | Without page reload |
| URL Param   | `/rating/5/`        |
| Query Param | `?search=bittu`     |


# Django ORM `annotate()` – Kya Hota Hai & Kyu Use Hota Hai?

## ✅ annotate() Kya Hota Hai?

`annotate()` Django ORM ka function hai jo **queryset ke har record ke saath extra calculated field add** karta hai — mostly aggregation ke through.

Simple words me:

> annotate() = har row/object ke saath calculated value attach karna

Ye database level par calculation karta hai (SQL GROUP BY + aggregate).

---

## ✅ Kab Use Karte Hain?

Jab tumhe chahiye:

- Per object count
- Per object total
- Per object average
- Related records ka count
- Calculated fields (sum/avg/count)

---

## ✅ annotate vs aggregate (Important Difference)

| Function | Result |
|-----------|------------|
aggregate() | Single summary value return |
annotate() | Har record ke saath value add |

---

## ✅ Example Models (Use Case)

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    price = models.IntegerField()
    author = models.ForeignKey(Author, on_delete=models.CASCADE)


#### ✅ Example 1 — Har Author ke Total Books Count
```python
from django.db.models import Count

authors = Author.objects.annotate(
    book_count=Count("book")
)

for a in authors:
    print(a.name, a.book_count)
```

👉 Output:
Rahul → 5
Amit → 2

#### ✅ Example 2 — Har Author ki Books ka Avg Price
```python
from django.db.models import Avg

authors = Author.objects.annotate(
    avg_price=Avg("book__price")
)
```

#### ✅ Example 3 — Sum of Related Field
```python
from django.db.models import Sum

authors = Author.objects.annotate(
    total_price=Sum("book__price")
)
```

#### ✅ Example 4 — Filter Using Annotated Field
```python
authors = Author.objects.annotate(
    book_count=Count("book")
).filter(book_count__gt=3)
```

👉 Sirf wo authors jinke paas >3 books hain

#### ✅ Example 5 — Multiple Annotate Fields
```python
authors = Author.objects.annotate(
    book_count=Count("book"),
    avg_price=Avg("book__price")
)
```

## Django ORM `annotate()` – Deep Detail Explanation (Interview Ready)

### ✅ Simple Definition

`annotate()` Django ORM ka method hai jo **har record ke saath ek extra calculated field add karta hai** — ye calculation usually aggregation (Count, Sum, Avg, Max, Min) se hoti hai.

`annotate()` ka matlab hota hai **database ke har record ke sath extra information jod dena**  
jo normally model me store nahi hoti, balki **calculate hoti hai**.

👉 Ye calculation database level par hoti hai — Python loop se nahi.

---

### ✅ Real Meaning (Easy Language)

Soch:

Tumhare paas records hain — aur tum chahte ho:

- Har author ke kitni books hain
- Har product ka total sales
- Har user ke kitne orders
- Har post ke kitne comments

Ye sab **per-row calculation** hai → ye kaam `annotate()` karta hai.

---

# Idempotency kya hoti hai?

Idempotency ka matlab hai ki agar aap ek hi request ko database ya server par baar-baar (multiple times) bhejte hain, to uska nateeja (side-effect) hamesha wahi rahega jo pehli baar me tha. Server ki state par koi extra farq nahi padega.

> In short: 1 Request = Same Result. 100 Same Requests = Still Same Result.

### Real-World Example: Payment Gateway:-
Maan lo aap ek E-commerce website par kapde khareed rahe hain. Aapne "Pay Now" button par click kiya.

- Aapka internet slow tha, to aapne gusse me ya galti se "Pay Now" button par 3 baar click kar diya.
- Agar API Idempotent NAHI hai: Aapke bank account se 3 baar paise kat jayenge (Kyunki server ne har click ko ek naya order mana).
- Agar API Idempotent HAI: Pehli request par paisa katega, aur baaki ki 2 requests ko server pehchan lega ki yeh wahi purani request hai, aur unhein bina paise kaate skip kar dega (ya purana hi response return kar dega).

### Backend Me Isko Implement Kaise Karte Hain? (System Design Perspective)
Interviewer poochega: "Agar POST idempotent nahi hai, to aap Payment API ko idempotent kaise banayenge?"

- Frontend ek Unique Key banata hai: Jab bhi frontend koi sensitive request (jaise payment) bhejta hai, to wo request ke header me ek unique random string bhejta hai, jise Idempotency-Key (ya UUID) kehte hain.
- Backend check karta hai: Backend (Django/Node) me request aate hi, hum sabse pehle us Key ko Redis (ya cache database) me check karte hain.
- Pehli baar Request aayi: Key database me nahi milti. Hum request process karte hain, payment karte hain, aur us Key ko response ke sath Redis me save kar dete hain (expiry time ke sath, jaise 5 mins).
- Dubara Request aayi (Duplicate): Agar user ne phir se click kiya, to backend dekhta hai ki yeh Idempotency-Key to pehle se Redis me maujood hai! Backend bina database ya payment gateway ko hit kiye, purana wala response hi turant wapas bhej deta hai.

### HTTP Methods Aur Idempotency:-

| HTTP Method | Idempotent? | Technical Description & Reason | Real-world Analogy / Example |
| :--- | :---: | :--- | :--- |
| **GET** | **YES** | Used only to fetch data. It does not alter the server state or database records. Making 1 or 100 requests returns the same resource status without side-effects. | Viewing a product page on Amazon. Refreshing the page 10 times doesn't change the price or stock. |
| **PUT** | **YES** | Used to update/replace an entire resource. If you send the same payload multiple times, the resource is updated to that exact state every time. Server state stays identical after the first call. | Setting your profile age to `25`. Updating it to `25` multiple times leaves your age at `25`. |
| **DELETE** | **YES** | Used to remove a resource. The first request deletes the item (State changes: Active -> Deleted). Subsequent identical requests find nothing to delete, returning `404 Not Found`, but the system state doesn't change further. | Erasing a specific whiteboard entry. Scraping an already empty spot 5 more times does nothing new. |
| **POST** | **NO** | Used to create a new resource. Every distinct request creates a brand new record in the database. Multiple identical requests result in multiple duplicate entries. | Clicking the 'Submit Order' or 'Pay Now' button multiple times, creating multiple separate charges or orders. |
| **PATCH** | **NO / DEPENDS** | Used for partial updates. It *can* be idempotent, but isn't by default. For example, if the patch operation is incremental (`"age": "age + 1"`), repeating it changes the state each time. | Toggling a status or incrementing a counter. Each click adds to the previous value. |
| **HEAD** | **YES** | Identical to GET, but it only retrieves the response headers (meta-information) without the response body. Safe and has no side effects. | Checking the last-modified date of a file without downloading the file itself. |
| **OPTIONS** | **YES** | Used to describe the communication options (like allowed HTTP methods or CORS configurations) for the target resource. Read-only and safe. | Pre-flight request sent by browsers to verify server permissions before sending actual data. |

# Django me WSGI aur ASGI kya ha or inke beech kya farq hai? Aap ASGI and WSGI ka use kab karenge?
Django me WSGI aur ASGI dono hi Web Servers aur aapke Django application ke beech ka "medium" (ya interface) hain. Jab koi user aapki website par aata hai, to server tak request aati hai, aur yeh interfaces us request ko Django code tak pahunchate hain.

### 1. WSGI Kya Hai? (Web Server Gateway Interface)

- Yeh kaise kaam karta hai? Yeh "One Request per Thread" ke rule par kaam karta hai. Matlab agar ek request aayi, to ek thread usko handle karega. Jab tak wo request poori nahi hoti (jaise database se data aana), tab tak wo thread busy rahega aur doosri request nahi le sakta.

- Kab use karein? Agar aap ek normal web application bana rahe hain (jaise blog, e-commerce, ya CMS) jahan sirf standard HTTP requests (GET, POST) aati hain aur data turant load ho jata hai.

### 2. ASGI Kya Hai? (Asynchronous Server Gateway Interface)

- Yeh kaise kaam karta hai? Yeh ek sath multiple requests ko bina kisi thread ko block kiye handle kar sakta hai. Agar ek request database ka wait kar rahi hai, to server us waqt doosri request ko process karne lag jata hai. Yeh sirf HTTP hi nahi, balki WebSockets aur Chat protocols ko bhi support karta hai.

- Kab use karein? Agar aapko real-time features banane hain—jaise Chat Applications, Live Notifications, Real-time Dashboards, ya WebSockets ka use karna ho.

### Aapko kab kaun sa use karna chahiye?

> WSGI ka use kab karein:
- Standard CRUD Applications: Agar aapka project ek basic CRUD application hai jahan user data mangta hai aur aap respond karte hain.
- No Real-time Requirement: Jab aapko long-polling ya continuous server connection (WebSockets) ki zaroorat nahi hai.
- Legacy Projects: Agar aap kisi purane Django project (Django 2.x ya usse pehle) par kaam kar rahe hain.

> ASGI ka use kab karein:
- Real-time Applications: Agar aap Django Channels ka use karke ek Chat App ya Live Delivery Tracking System bana rahe hain.
- Long-running Connections: Jab frontend aur backend ke beech continuous connection chahiye (jaise Stock Market live charts).
- High Concurrency: Jab aap chahte hain ki kam resources (RAM/CPU) me aapka server ek sath hazaron requests handle kar sake bina crash hue.

### WSGI vs ASGI: Mukhyo Farq

| Feature | WSGI | ASGI |
| :--- | :--- | :--- |
| **Full Form** | Web Server Gateway Interface | Asynchronous Server Gateway Interface |
| **Nature** | **Synchronous** (Ek baar me ek kaam line se) | **Asynchronous** (Ek sath kai kaam bina block kiye) |
| **Protocols** | Sirf **HTTP** ko support karta hai. | **HTTP + WebSockets** dono ko support karta hai. |
| **Speed / Performance** | Heavy traffic aur long-running requests me slow ho sakta hai. | Bohot fast hai, khaaskar jab real-time data transfer ho raha ho. |
| **Default Servers** | Gunicorn, uWSGI | Daphne, Uvicorn |

---

## Technical Context for Interviews

### WSGI (Synchronous Architecture)
* **How it works:** It follows a **one-request-per-thread** model. If a request is waiting for a database query or a third-party API response, the entire thread remains blocked.
* **Best used for:** Standard CRUD operations, traditional e-commerce platforms, blogs, and content management systems.

### ASGI (Asynchronous Architecture)
* **How it works:** Built as a successor to WSGI, it handles multiple requests concurrently using an event loop (asyncio). It can pause a request while waiting for background I/O operations and process other incoming requests in the meantime.
* **Best used for:** Real-time chat applications, live notifications, video streaming platforms, or any application requiring persistent WebSocket connections.