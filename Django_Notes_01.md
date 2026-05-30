# Django Notes

## 🌐 Q1. Django kya hai?

### ✔ Simple Answer  
Django ek **high-level Python web framework** hai jo developers ko **fast, secure, aur scalable web applications** banane me madad karta hai. Ye **rapid development** aur **clean, maintainable code** par focus karta hai.

---

### 🔍 Detailed Explanation  
Django ka basic maksad hai web development ko **asaan, fast aur less coding ke sath** possible banana.  
Isme **built-in modules & tools** milte hain jisse common web tasks manually likhne ki zarurat nahi padti, jaise:

- user authentication  
- admin panel  
- database ORM  
- form handling  
- routing (URL management) etc.

Django **MVC architecture ke ek variant** par kaam karta hai jise hum **MVT (Model–View–Template)** kehte hain.

---

### ⭐ Django Key Features

| Feature | Description |
|--------|-------------|
| **MVT Architecture** | Model–View–Template structure for clean & organized code |
| **Fast Development** | Project quickly ban sakta hai due to built-in tools |
| **Secure** | Prevents SQL injection, XSS, CSRF, Clickjacking etc. |
| **Scalable** | High-traffic websites ke liye suitable |
| **ORM (Object Relational Mapping)** | Python objects se DB queries handle karta hai |
| **Built-in Admin Panel** | Auto-generated admin interface without extra coding |
| **Batteries Included** | Forms, Auth, Sessions, Email, File Upload — sab available |
| **Cross-Platform** | Windows, Linux, macOS sab par run karta hai |
| **Third-Party Packages Support** | REST API, Social Login, Payment Gateways etc. |

---

1. **MVT Architecture:**  
   Ye data, business logic aur UI ko alag-alag handle karta hai (Model, View, Template).

2. **ORM (Object Relational Mapper):**  
   Database ke sath interact karne ke liye SQL likhne ki zarurat nahi hoti.  
   Django ORM automatically Python objects ko database tables se map karta hai.

3. **Admin Interface:**  
   Django automatically ek ready-to-use **admin panel** generate karta hai jisse data manage karna easy ho jata hai.

4. **Security:**  
   Django me built-in protection hoti hai against **SQL Injection, XSS, CSRF,** aur **clickjacking** attacks.

5. **Scalability:**  
   Django large-level projects ke liye design hua hai, jaise Instagram, Pinterest etc.

6. **Built-in Authentication System:**  
   User login, logout, password management ke liye ready-made system milta hai.

7. **Rapid Development:**  
   Pre-built tools aur libraries hone ki wajah se development speed kaafi tez hoti hai.

8. **URL Routing System:**  
   Clean aur SEO-friendly URLs define karne ke liye URL dispatcher system hota hai.

9. **Template Engine:**  
   Dynamic HTML pages generate karne ke liye powerful templating system use hota hai.

10. **Community Support:**  
    Django ek open-source project hai jiska community support kaafi strong hai.

### 💡 Extra Info / Interview Booster

- Django ko **“Batteries Included Framework”** bhi kaha jata hai kyunki web development ke most common features already included hote hain.
- Django **loosely coupled architecture** follow karta hai: har component independent hota hai.
- Django ka source code **open-source** hai aur **BSD license** ke under licensed hai.
- Django **DRY Principle (Don’t Repeat Yourself)** follow karta hai, code reusability par focus karta hai.

---

### 🧠 Famous Companies Using Django
- Instagram  
- Pinterest  
- Spotify  
- YouTube (Some Components)  
- Disqus  

---

### 📌 One-Line Summary (Interview Friendly)
> **Django ek high-level Python web framework hai jo secure, scalable, aur fast web development ke liye built-in tools ke saath MVT architecture follow karta hai.**

---

# Django Architecture Diagram


```text
                 ┌────────────────────────┐
                 │        Browser /        │
                 │       User Request      │
                 └────────────┬────────────┘
                              │
                              ▼
                   ┌────────────────────┐
                   │      URL Router     │
                   │  (urls.py patterns) │
                   └────────────┬────────┘
                              │
                              ▼
                   ┌────────────────────┐
                   │        View         │
                   │     (views.py)      │
                   └────────────┬────────┘
                              │
             ┌────────────────┴────────────────┐
             │                                 │
             ▼                                 ▼
   ┌────────────────────┐          ┌──────────────────────────┐
   │       Model         │          │        Template          │
   │     (models.py)     │          │         (.html)          │
   │  (Database Layer)   │          │ (Frontend Presentation)  │
   └────────────┬────────┘          └─────────────┬───────────┘
                │                                 │
                └─────────────────────────────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │      HTTP Response      │
                 │      back to User       │
                 └────────────────────────┘

Django MVT architecture me URL request View ko trigger karti hai, View Model se data retrieve karta hai, aur Template HTML generate karke user ko response deta hai.

# 🧩 MVC Architecture (Model - View - Template)

### ❖ Question  
**Django ke MVT (Model–View–Template) architecture ko har part ke sath detail me explain karo.**

---

## 🔥 Django MVT Architecture – Detailed Explanation

Django ka MVT architecture **Model–View–Template** par based hota hai.  
Ye MVC jaisa hi hai, bas View aur Controller ke naam Django ke hisaab se alag hote hain.

---

## ✔ 1️⃣ Model — **Database / Data Layer**

**Model database ka structure represent karta hai.**  
Isme hum tables, fields, constraints aur relations define karte hain using Python code.

📌 Model ki zimmedari:
- Database tables create karna
- Insert / Update / Delete / Fetch operations handle karna
- Data validations handle karna
- Business logic related to database store karna

📌 Example — `models.py`
```python
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.FloatField()
    stock = models.IntegerField()

```

## ✔ 2️⃣ View — Business Logic / Request Handler

**View wo part hai jahan hum business logic likhte hain.**
Ye URL se request lega, zarurat ho to Model ko call karega, aur response generate karega.

📌 View ki zimmedari:

* Request receive karna
* Model ke sath interaction karna
* Data ko Template ko dedena
* Response return karna (HTML / JSON / PDF / CSV / etc.)

📌 Example — `views.py`

```python

from django.shortcuts import render
from .models import Product

def product_list(request):
    products = Product.objects.all()
    return render(request, "product_list.html", {"products": products})
```


### ✔ 3️⃣ Template — Presentation / UI Layer

**Templates final output user ko show karte hain. Mostly HTML + CSS + JS.**

📌 Template ki zimmedari:

* View se aaya hua data dikhana
* Page ka design / formatting / UI handle karna
* Django Template Language (DTL) ke sath dynamic HTML generate karna

📌 Example — `product_list.html`

```html
<h2>Product List</h2>
{% for item in products %}
    <p>{{ item.name }} — ₹{{ item.price }}</p>
{% endfor %}
```


```python
from django.db import models


class SampleModel(models.Model):

    # ---------- Text Fields ----------
    title = models.CharField(max_length=100)
    description = models.TextField()
    email = models.EmailField()
    website = models.URLField()
    slug = models.SlugField()
    unique_id = models.UUIDField()

    # ---------- Number Fields ----------
    age = models.IntegerField()
    big_count = models.BigIntegerField()
    positive_points = models.PositiveIntegerField()
    positive_small = models.PositiveSmallIntegerField()
    small_num = models.SmallIntegerField()
    rating = models.FloatField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    auto_no = models.AutoField(primary_key=False)
    auto_big_no = models.BigAutoField(primary_key=False)

    # ---------- Date & Time Fields ----------
    dob = models.DateField()
    login_time = models.TimeField()
    register_datetime = models.DateTimeField()
    duration = models.DurationField()

    # ---------- File & Media Fields ----------
    document = models.FileField(upload_to="docs/")
    image = models.ImageField(upload_to="images/")
    binary_file = models.BinaryField()

    # ---------- Relational Fields ----------
    category = models.ForeignKey("Category", on_delete=models.CASCADE)
    profile = models.OneToOneField("Profile", on_delete=models.CASCADE)
    tags = models.ManyToManyField("Tag")

    # ---------- Advanced Fields ----------
    json_data = models.JSONField()
    ip_address = models.GenericIPAddressField()
    # For PostgreSQL only
    # array_data = models.ArrayField(models.CharField(max_length=100))

```

# ❖ models.ForeignKey me on_delete kya hota hai?

Django me jab hum ForeignKey use karte hain, iska matlab hai ki ek table dusre table se linked hai.

Lekin agar parent table ka record delete ho jaye, to Django ko pata nahi hota ki child table me us record ka kya karna hai —
isi liye hum on_delete set karte hain.

📌 on_delete batata hai ki parent record delete hote time child record ke sath kya hona chahiye.

```python
    class Order(models.Model):
    customer = models.ForeignKey(Customer, on_delete=models.CASCADE)
```

### 🔥 on_delete ke important options (simple language me)

| Option        | Matlab                                      | Used When                 |
| ------------- | ------------------------------------------- | ------------------------- |
| `CASCADE`     | Parent delete → child bhi delete            | Dependent data ho         |
| `SET_NULL`    | Parent delete → child field NULL ho         | Relation optional ho      |
| `SET_DEFAULT` | Parent delete → default value set ho        | Default fallback ho       |
| `PROTECT`     | Delete ko rok dega (error dega)             | Related data protected ho |
| `RESTRICT`    | Delete rok dega (modern version of PROTECT) | Strong data safety ho     |
| `DO_NOTHING`  | Kuch mat karo, but error aa sakta hai       | Rare use                  |
| `SET()`       | Custom value set karna                      | Special custom logic      |




```python
class Student(models.Model):
    name = models.CharField(max_length=50)

class Fee(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    amount = models.IntegerField()

# Agar Student delete hoga → Fee bhi delete ho jayega (because CASCADE).
```

# 🧩 Django Views (Business Logic / Presentation Layer)

## 🌐 Q1. Django View kya hai?

**Definition:**  
Django me **View** wo layer hai jo **user ke request ko handle** karti hai aur **response return** karti hai.  
Ye decide karta hai ki **konsa data** display karna hai aur **kaise display** karna hai.  

> “View ek Python function ya class hoti hai jo HTTP request ko process karke HTTP response generate karti hai.”

## ⚙️ Django Views ke Key Points

1. **Request Handle karna:**  
   - View user ke **HTTP request** ko receive karta hai.

2. **Data Process karna:**  
   - Agar database se data fetch karna ho to **Model** ke sath interact karta hai.

3. **Template Render karna:**  
   - Data ko **HTML template** me pass karta hai aur render karke user ko return karta hai.

4. **Response Return karna:**  
   - Final output: HTML, JSON, file download, redirect, etc.

---

### 1. **Function-Based Views (FBV)**
    
- Simple Python function jo request handle karta hai.  
- Beginners ke liye easy aur quick.  

**Example:**
```python
from django.http import HttpResponse

def home(request):
    return HttpResponse("Welcome to Django App")
```

### 2. **Class-Based Views (CBV)**

**Explanation:**  
- Django me views ko **classes ke form me** bhi likha ja sakta hai.  
- CBVs ka advantage ye hai ki ye **reusable aur organized** hote hain.  
- Django me predefined generic CBVs available hain:  
  `ListView`, `DetailView`, `CreateView`, `UpdateView`, `DeleteView`

**Example:**
```python
from django.views import View
from django.http import HttpResponse

class HomeView(View):
    def get(self, request):
        return HttpResponse("This is a Class-Based View")

## 🔥 Most Common Response Types

| Response Type    | Purpose                                        | Example                                        |
| ---------------- | ---------------------------------------------- | ---------------------------------------------- |
| **HttpResponse** | Normal text / HTML return karne ke liye        | `return HttpResponse("Hello")`                 |
| **JsonResponse** | API ya AJAX ke liye JSON data bhejne ke liye   | `return JsonResponse({"name": "Amit"})`        |
| **render**       | HTML template ke saath data show karne ke liye | `return render(request, "home.html", context)` |
| **redirect**     | Kisi doosre URL ya page par bhejne ke liye     | `return redirect("login")`                     |
| **FileResponse** | File download ya PDF/image bhejne ke liye      | `return FileResponse(open("file.pdf", "rb"))`  |

```python
from django.http import HttpResponse, JsonResponse, FileResponse
from django.shortcuts import render, redirect

def my_view(request):

    # 1. Normal text/html
    # return HttpResponse("<h1>Hello World!</h1>")

    # 2. JSON Response
    # return JsonResponse({"status": "success", "id": 10})

    # 3. Render Template
    # return render(request, "home.html", {"title": "Home"})

    # 4. Redirect to another page
    # return redirect("login")

    # 5. File Response (download)
    return FileResponse(open("sample.pdf", "rb"))
```
## **🔄 View ka Life Cycle**

Browser (Client)
      ↓
URLConf (urls.py)
      ↓
View (Function/Class)
      ↓
Model (Data Access)
      ↓
Template (HTML Render)
      ↓
Response (Back to Client)
**Explanation:**  
1. **Browser (Client):** User browser se HTTP request send karta hai  
2. **URLConf (urls.py):** URL pattern match karke request ko appropriate view ke paas forward karta hai  
3. **View (Function/Class):** Request process karta hai aur data fetch/logic execute karta hai  
4. **Model (Data Access):** Agar data required ho to model ke through database se fetch/update karta hai  
5. **Template (HTML Render):** Data ko template me render karke HTML page generate karta hai  
6. **Response (Back to Client):** Final HTML/JSON/redirect response user ke browser me return hota hai

**Short Interview Line:**  
> “Django me request browser se aati hai, urls.py me route hoti hai, view process karta hai, model se data fetch hota hai, template me render hota hai aur final response user ko return hota hai.”


# 🔄 Django Backend → Frontend Data Flow

---

## 1. **Backend (Models + Views)**

### Step 1: Model me data store karna
- Data database me **Models** ke through store hota hai.
```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.FloatField()


### Step 2: View me data fetch karna
* View Model se data fetch karta hai aur template ko pass karta hai.

from django.shortcuts import render
from .models import Product

def product_list(request):
    products = Product.objects.all()  # ORM query
    return render(request, 'products.html', {'products': products})


### step 3: Frontend (Templates)
* Templates me context data (dictionary) ko render karte hain using {{ variable_name }}.
* Template tags se loops aur conditions bhi handle hote hain.

<h1>Product List</h1>
<ul>
{% for product in products %}
    <li>{{ product.name }} - ₹{{ product.price|floatformat:2 }}</li>
{% empty %}
    <li>No products available</li>
{% endfor %}
</ul>

##### Explanation:

    * products → View se aaya hua queryset
    * {% for product in products %} → loop for each product
    * {{ product.name }} aur {{ product.price }} → data render karna

### **Backend → Frontend Flow Diagram**

Database (Model)
      ↓
View (Fetch + Process)
      ↓
Context Dictionary
      ↓
Template (Render HTML)
      ↓
User Browser (Frontend)
# 🧩 Django Context Data → Frontend UI

---

## 1. **Backend: Context Data Pass Karna**

- Views me **Python dictionary** create kar ke template ko pass karte hain.  
- Ye **server-side rendering** ka method hai.

```python
from django.shortcuts import render
from .models import Product

def product_list(request):
    products = Product.objects.all()  # Queryset
    context = {'products': products}  # Context dictionary
    return render(request, 'products.html', context)


## Template

```html
<h1>Product List</h1>
<ul>
{% for product in products %}
    <li>{{ product.name }} - ₹{{ product.price|floatformat:2 }}</li>
{% empty %}
    <li>No products available</li>
{% endfor %}
</ul>

# 🧩 Django Model (Data Layer)

---

## 1. **Model kya hai?**

**Definition:**  
- Django me **Model** ek Python class hoti hai jo **database table** ko represent karti hai.  
- Ye **business logic ka data structure** define karta hai aur ORM (Object-Relational Mapping) ke through database ke sath interact karta hai.  

> “Django Model ek blueprint hai jo database table aur fields define karta hai aur database operations ko Python code ke through handle karta hai.”

---

## 2. **Model ke Key Features**

1. **Database Table Representation:**  
   - Har model ek **table** banata hai database me  
   - Model ke fields → Table ke columns

2. **ORM Integration:**  
   - Django ORM automatically Python objects ko SQL queries me convert karta hai

3. **Data Validation:**  
   - Model fields ke types aur constraints ke through automatic data validation

4. **Relationships Support:**  
   - One-to-One, One-to-Many, Many-to-Many relationships define kar sakte hain  

5. **Custom Methods & Properties:**  
   - Model me functions define karke business logic implement kar sakte ho  

---

## 3. **Basic Model Example**

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)   # VARCHAR(100)
    price = models.FloatField()               # Float column
    stock = models.IntegerField(default=0)    # Integer column
    is_active = models.BooleanField(default=True) # Boolean column
    created_at = models.DateTimeField(auto_now_add=True) # Auto timestamp

    def __str__(self):
        return self.name
```
## 4. **Relationships Example**

```python
class Category(models.Model):
    name = models.CharField(max_length=50)

class Product(models.Model):
    name = models.CharField(max_length=100)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)


* ForeignKey → One-to-Many relationship (Many products → One category)
* on_delete=models.CASCADE → Category delete hone par related products bhi delete ho jaayenge

### 5. **Model Inheritance**

**Explanation:**  
- Django me **model inheritance** se **code reuse** aur **modular design** possible hota hai.  
- Ye allow karta hai ki ek base model ke fields aur methods ko multiple child models me reuse kiya ja sake.  
- Teen main types hote hain: **Abstract Base Class**, **Multi-Table Inheritance**, aur **Proxy Model**.

---

#### 🔹 1. **Abstract Base Class**

- Base class sirf **fields aur methods provide karti hai**, table create nahi hota.  
- Child class apne liye separate table create karti hai aur base class ke fields inherit karti hai.

**Example:**
```python
class BaseModel(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        abstract = True

class Product(BaseModel):
    name = models.CharField(max_length=100)
```

#### 🔹 **Multi-Table Inheritance**

**Explanation:**  
- Parent aur child ke liye **separate database tables** create hote hain.  
- Child table me parent ka **primary key** bhi store hota hai, taaki relation maintain rahe.  
- Ye inheritance **real table structure** maintain karta hai aur relational queries easy banata hai.

**Example:**
```python
class Product(models.Model):
    name = models.CharField(max_length=100)

class Book(Product):
    author = models.CharField(max_length=100)
```

#### 🔹 **Proxy Model**

**Explanation:**  
- Proxy Model me **database table same rehta hai**, lekin Python-level **behavior, methods, ya ordering** modify ki ja sakti hai.  
- Data store **existing table** me hota hai, sirf **queryset behavior** ya extra methods add karte hain.  
- Useful jab **data structure change nahi karna** hai, lekin **functionality customize** karni hai.

**Example:**
```python
class ProductProxy(Product):
    class Meta:
        proxy = True
        ordering = ['name']


| Type                        | Table          | Use Case                   |
| --------------------------- | -------------- | -------------------------- |
| **Abstract Base Class**     | ❌ Parent table | Common fields reuse        |
| **Multi-Table Inheritance** | ✔ Both tables  | Parent-child data required |
| **Proxy Model**             | ❌ New table    | Only behavior change       |


# 🔔 Django Signals


**Explanation:**  
- **Signals** se hum **model ke events** ko listen aur handle kar sakte hain.  
- Ye automation aur hooks implement karne me help karta hai.  
- Common signals: `pre_save`, `post_save`, `pre_delete`, `post_delete`

**Use Case:**  
- Jab bhi koi model save ya delete hota hai, automatically koi task perform karna, jaise:
  - Notification send karna  
  - Logging karna  
  - Related objects update karna  

**Example:**
```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import Product

@receiver(post_save, sender=Product)
def after_product_save(sender, instance, **kwargs):
    print(f"Product saved: {instance.name}")

## 🔥 Most Important Built-in Signals

| Signal Name        | Event                                 |
| ------------------ | ------------------------------------- |
| `pre_save`         | Save se pehle                         |
| `post_save`        | Save ke baad                          |
| `pre_delete`       | Delete se pehle                       |
| `post_delete`      | Delete ke baad                        |
| `m2m_changed`      | Many-to-Many relation change hone par |
| `request_started`  | Request start hone par                |
| `request_finished` | Request end hone par                  |
| `post_migrate`     | Migrations ke baad                    |


## ✔ Simple Example (Interview Friendly)

```python
# users/signals.py
from django.db.models.signals import post_save
from django.contrib.auth.models import User
from django.dispatch import receiver

@receiver(post_save, sender=User)
def send_welcome_email(sender, instance, created, **kwargs):
    if created:  # New user created
        print("Welcome Email Sent to:", instance.email)


#--------------------------------

from django.apps import AppConfig

class UsersConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'users'

    def ready(self):
        import users.signals
```

# 🧩 Django Jinja Templates

---

## 1. **Jinja Template kya hai?**

**Definition:**  
- **Jinja2** ek Python template engine hai jo Django me optional use kiya ja sakta hai.  
- Ye Django ke default template language se **faster aur flexible** hai.  
- Syntax similar hai, lekin **advanced features aur performance** better hoti hai.

> “Jinja2 ek template engine hai jo HTML + Python-like syntax ke sath dynamic content render karta hai, aur Django me optional replace ke liye available hai.”

---

## 2. **Django Default Templates vs Jinja2**

| Feature | Django Template | Jinja2 Template |
|---------|----------------|----------------|
| Syntax | `{% for %}`, `{% if %}`, `{{ variable }}` | `{% for %}`, `{% if %}`, `{{ variable }}` (similar) |
| Speed | Slower for complex loops | Faster, optimized rendering |
| Filters | Limited built-in filters | More built-in filters |
| Extensibility | Custom tags possible | Custom filters & global functions easily |
| Debugging | Basic error messages | Better error messages and template debugging |

---

## 3. **Jinja2 Basic Example**

**View (Django backend):**
```python
from django.shortcuts import render
from .models import Product

def product_list(request):
    products = Product.objects.all()
    return render(request, 'products.jinja', {'products': products})

### Template (products.jinja):

```html
<h1>Product List (Jinja)</h1>
<ul>
{% for product in products %}
    <li>{{ product.name }} - ₹{{ "%.2f"|format(product.price) }}</li>
{% else %}
    <li>No products available</li>
{% endfor %}
</ul>

# 🧩 Django Migrations

---

## 1. **Migration kya hai?**

**Definition:**  
- Django me **Migration** ek way hai jo **database schema ko track aur manage** karta hai.  
- Jab bhi hum **models me changes** karte hain, Django migration files create karta hai jo **database me tables aur columns update** karte hain.  

> “Migration ek version control system jaisa hai jo database structure ko Python code ke changes ke sath sync karta hai.”

---

## 2. **Migration ke Key Features**

1. **Automatic Database Changes:**  
   - Models me changes ke basis par migration generate hoti hai  
   - Database manually modify karne ki zarurat nahi

2. **Version Control for DB:**  
   - Har migration ek **step/version** hoti hai  
   - `migrate` command se specific migration apply ki ja sakti hai

3. **Cross-Database Support:**  
   - Django migration system multiple databases ke liye bhi kaam karta hai

4. **Rollback Support:**  
   - Migration undo karke database ko pehle ke state me laa sakte ho  

---

## 3. **Migration Commands**

| Command | Description |
|---------|------------|
| `python manage.py makemigrations` | Model changes detect karke migration file create karta hai |
| `python manage.py migrate` | Database me migration apply karta hai |
| `python manage.py showmigrations` | Applied aur pending migrations list karta hai |
| `python manage.py sqlmigrate <app> <migration>` | SQL commands jo migration execute karegi, show karta hai |

---

## 4. **Example Flow**

### Step 1: Model create karna
```python
# models.py
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.FloatField()


# 🧩 Django Shell

---

## 1. **Django Shell kya hai?**

**Definition:**  
- Django shell ek **interactive Python shell** hai jo **Django project ke context ke sath** run hoti hai.  
- Isme hum **models, ORM queries, aur project ke functions** ko directly test aur explore kar sakte hain.  

> “Django shell ek REPL (Read-Eval-Print Loop) environment hai jisme Django project ke objects aur database ko interactively manage kiya ja sakta hai.”

---

## 2. **Shell ke Key Features**

1. **Interactive Testing:**  
   - Models aur queries ko test karna without running server

2. **Database Access:**  
   - ORM ke through database me data fetch, create, update, delete kar sakte ho

3. **Debugging Tool:**  
   - Functions aur scripts ko real-time me run karke debugging kar sakte ho

4. **Custom Scripts:**  
   - Project ke environment me custom Python scripts run karna easy

---

## 3. **Shell Commands**

| Command | Description |
|---------|------------|
| `python manage.py shell` | Default Django interactive shell (Python shell) |
| `python manage.py shell_plus` | (If django-extensions installed) Auto import models and project context |
| `exit()` / `Ctrl + D` | Shell se exit karne ke liye |

---

## 🧩 Django Shell: Data Create Karna

---

### 1. **Step 1: Shell Open Karna**

```bash
python manage.py shell

```
```python
# Models Import Karna
from myapp.models import Product

#Data Create Karna
product = Product.objects.create(
    name="Laptop",
    price=50000,
    stock=10,
    is_active=True
)
```

### Another Method: Object Initialization + save()

```python
product = Product(
    name="Mobile",
    price=20000,
    stock=50,
    is_active=True
)
product.save()
```
# Bulk Create
```python
products = [
    Product(name="Tablet", price=15000, stock=30),
    Product(name="Monitor", price=10000, stock=20),
]
Product.objects.bulk_create(products)
```

# Get All data.
```python
Product.objects.all()
```
# Get single data
```python
Product.objects.all()[1]
```
# Get single data and fildes name
```python
Product.objects.all()[1].name
Product.objects.all()[1].price
Product.objects.all()[0].stock


# 🧩 Django Shell: Function Call Karna

---

## 1. **Step 1: Shell Open Karna**

```bash
python manage.py shell



```python
from myapp.models import Product

# Model Method

product = Product.objects.first()  # First product fetch
discounted_price = product.get_discounted_price()
print(discounted_price)

#===============================================================

# Run function using sheel

from myApp.views import *

myfunname();
```

# 🧩 Django Shell: CRUD Operations

**Model Example (Product):**
```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.FloatField()
    stock = models.IntegerField(default=0)
    is_active = models.BooleanField(default=True)

    def __str__(self):
        return self.name



### **1️⃣ Create (Data Insert)**

# Method 1: create() method
```python
from myapp.models import Product

product1 = Product.objects.create(
    name="Laptop",
    price=50000,
    stock=10,
    is_active=True
)
```
# Method 2: Object Initialization + save()
```python
product2 = Product(
    name="Mobile",
    price=20000,
    stock=50,
    is_active=True
)
product2.save()
```

# Bulk Create

```python
products = [
    Product(name="Tablet", price=15000, stock=30),
    Product(name="Monitor", price=10000, stock=20),
]
Product.objects.bulk_create(products)


### **2️⃣ Read (Data Fetch / Query)**


```python
# Fetch All Products
all_products = Product.objects.all()
print(all_products)

# Filter Products
expensive_products = Product.objects.filter(price__gt=20000)
print(expensive_products)

# Get Single Product
laptop = Product.objects.get(name="Laptop")
print(laptop.name, laptop.price)

```

### **3️⃣ Update (Modify Data)**


```python
# Update Single Object
laptop = Product.objects.get(name="Laptop")
laptop.price = 48000
laptop.stock = 15
laptop.save()

# Update Using QuerySet
Product.objects.filter(price__lt=20000).update(is_active=False)

```

### **4️⃣ Delete (Remove Data)**


```python
# Delete Single Object
mobile = Product.objects.get(name="Mobile")
mobile.delete()

# Delete Multiple Objects
Product.objects.filter(is_active=False).delete()


```

### **5️⃣ Step 5: Verify Changes**


```python
Product.objects.all()  # Shows remaining active products
```

# 🧩 Django ORM (Object-Relational Mapping)

---

## 1. **ORM kya hai?**

**Definition:**  
- ORM ka full form hai **Object-Relational Mapping**  
- Django ORM ek **layer** hai jo **Python objects** ko **database tables** ke sath map karta hai.  
- Isse hum **SQL queries likhe bina** database operations kar sakte hain.

> “Django ORM Python objects ko database tables ke sath connect karta hai, aur Python syntax ke through CRUD aur complex queries perform karne deta hai.”

---

## 2. **ORM ke Key Features**

1. **Abstraction Layer:**  
   - SQL queries ki zarurat nahi, Python methods use karte hain  
   - Database independent code likhne me help

2. **CRUD Operations:**  
   - ORM se `create()`, `filter()`, `get()`, `update()`, `delete()` easily hota hai

3. **Relationships Management:**  
   - ForeignKey, OneToOneField, ManyToManyField handle karta hai

4. **QuerySets:**  
   - ORM me queries **QuerySet** objects return karte hain  
   - Lazy evaluation: Query tab run hoti hai jab result access hota hai

5. **Advanced Queries:**  
   - `annotate()`, `aggregate()`, `select_related()`, `prefetch_related()` support karta hai

---

## 3. **Basic ORM Examples**

### Create Object
```python
from myapp.models import Product

product = Product.objects.create(name="Laptop", price=50000, stock=10)


# 🧩 Django ORM Features with Examples

**Assume Model:**
```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.FloatField()
    stock = models.IntegerField(default=0)
    is_active = models.BooleanField(default=True)



```python
## filter()

# Price > 20000
expensive_products = Product.objects.filter(price__gt=20000)

# Price < 20000
expensive_products = Product.objects.filter(price__lt=20000)

# Price == 20000
expensive_products = Product.objects.filter(price=20000)

# Active products
active_products = Product.objects.filter(is_active=True)

## exclude()

# Price <= 20000
cheap_products = Product.objects.exclude(price__gt=20000)

## order_by()

# Price ascending
products_asc = Product.objects.all().order_by('price')

# Price descending
products_desc = Product.objects.all().order_by('-price')


## limit / slicing

# First 5 products
top_5 = Product.objects.all()[:5]

# Skip first 5 products
skip_5 = Product.objects.all()[5:]

# for between range (product between 10 to 50)
b/w_10_to_50 = Product.objects.all()[10:50]
## values() / values_list()

# Dictionary list
product_values = Product.objects.values('name', 'price')

# Tuple list
product_values_list = Product.objects.values_list('name', 'price')

## get()
laptop = Product.objects.get(name="Laptop")

## distinct()
unique_prices = Product.objects.values('price').distinct()

## raw()
products = Product.objects.raw('SELECT * FROM myapp_product WHERE price > %s', [20000])


```

# 🧩 Django ORM: Aggregate Functions

---

## 1️⃣ **Aggregate Function kya hai?**

**Definition:**  
- Aggregate functions **queryset ke multiple rows ka calculation** karte hain aur ek single value return karte hain. 
- Ye single row pr work krta ha.
- Filter function column pr filter lga kr row ko return kr dete ha.
- Django ORM me **`aggregate()` method`** ke through use kiye jaate hain.

> “Django aggregate functions queryset ke data ka summary / calculation karte hain, jaise sum, average, count, min, max, bina raw SQL likhe.”

---

## 2️⃣ **Common Aggregate Functions in Django ORM**

| Function | Description |
|----------|------------|
| `Sum()` | Column ke values ka total sum |
| `Avg()` | Column ke values ka average |
| `Count()` | Number of rows / objects |
| `Min()` | Column ka minimum value |
| `Max()` | Column ka maximum value |

---

## 3️⃣ **Example:**



```python
## Sum

total_stock = Product.objects.aggregate(Sum('stock'))
print(total_stock)  # {'stock__sum': 110}


## Average

avg_price = Product.objects.aggregate(Avg('price'))
print(avg_price)  # {'price__avg': 25000.0}


## Count

product_count = Product.objects.aggregate(Count('id'))
print(product_count)  # {'id__count': 5}


## Min / Max
min_price = Product.objects.aggregate(Min('price'))
max_price = Product.objects.aggregate(Max('price'))
print(min_price, max_price)  # {'price__min': 10000}, {'price__max': 50000}


### Multiple Aggregates Together
summary = Product.objects.aggregate(
    total_stock=Sum('stock'),
    avg_price=Avg('price'),
    max_price=Max('price')
)
print(summary)
# {'total_stock': 110, 'avg_price': 25000.0, 'max_price': 50000}


```
