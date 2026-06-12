# Django Interview Answers — settings.py, manage.py, INSTALLED_APPS

## ✅ settings.py ka Role Kya Hai?

**settings.py** Django project ka **main configuration file** hota hai. Isme project ki saari global settings defined hoti hain.

Ye file decide karti hai ki project ka behavior kaisa hoga.

### 🔹 settings.py me kya kya configure hota hai:

- Database configuration
- Installed apps list
- Middleware list
- Templates config
- Static & media files path
- Security settings (SECRET_KEY, DEBUG)
- Allowed hosts
- Language & timezone
- Authentication settings

### 🔹 Example Snippet

```python
DEBUG = True

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}
```

# What is manage.py – Django Me Kya Karta Hai?

### ✅ manage.py Kya Hai?

**manage.py** Django project ka command-line utility file hota hai  
jo project ko **run aur manage** karne ke liye use hota hai.

Ye file Django ke built-in commands ko project ke context me execute karti hai.

---

### ✅ manage.py Ka Main Kaam

- Django settings load karta hai
- Project environment setup karta hai
- Commands ko execute karta hai
- Developer ko CLI (command line) se project control dena

---

## ✅ manage.py Se Chalne Wale Important Commands

### 🔹 Development Server Run

```bash
python manage.py runserver
```
### 🔹 Database Migration

```bash
python manage.py makemigrations
python manage.py migrate
```

### 🔹 New App Create
```python
python manage.py startapp blog
```

### 🔹 Admin User Create
```python
python manage.py createsuperuser
```

### 🔹 Django Shell Open
```python
python manage.py shell
```

#### ✅ manage.py Internally Kya Karta Hai?

* DJANGO_SETTINGS_MODULE set karta hai
* settings.py load karta hai
* django.core.management ko call karta hai
* Commands ko proper Django context me run karata hai

# Django Me INSTALLED_APPS Kya Hota Hai?

## ✅ Definition

**INSTALLED_APPS** `settings.py` file ke andar ek list hoti hai  
jisme project me use hone wali saari Django aur custom apps register ki jaati hain.

Django sirf unhi apps ko load aur use karta hai jo INSTALLED_APPS me listed hoti hain.

---

## ✅ Simple Samajh

> INSTALLED_APPS = Django ko batane ki list ki kaunsi apps active hain project me.

Agar koi app yahan add nahi →  
❌ uske models load nahi honge  
❌ migrations run nahi hongi  
❌ admin me show nahi hogi  
❌ signals/config load nahi honge

---

## ✅ Example – INSTALLED_APPS Snippet

```python
# settings.py

INSTALLED_APPS = [
    # Django built-in apps
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Third-party apps
    "rest_framework",

    # Custom apps
    "blog",
    "shop",
    "users",
]
```
### ✅ INSTALLED_APPS Add Karne Ke Baad Kya Hota Hai?

* Jab app add karte ho:
* Django app ke models detect karta hai
* Migrations allow hoti hain
* Admin integration hoti hai
* AppConfig load hota hai
* Signals register hote hain
* Templates/static discovery hoti hai

#### Question: Agar app INSTALLED_APPS me add na ho to kya hoga?

* Django us app ko recognize nahi karega
* Database tables create nahi hongi
* Admin panel me show nahi hogi

# Django – ForeignKey vs OneToOneField vs ManyToManyField

Ye teeno Django model relationship fields hain  
jo tables (models) ke beech relation banane ke liye use hote hain.

Simple rule:

- ForeignKey → Many to One
- OneToOneField → One to One
- ManyToManyField → Many to Many

---

## ✅ 1️⃣ ForeignKey (Many → One)

### 🔹 Meaning
Ek table ke **multiple records** dusre table ke **ek record** se linked hote hain.

👉 Many objects → One object

### 🔹 Example Use Case
- Many Orders → One Customer
- Many Books → One Author
- Many Products → One Category

### 🔹 Example Code

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

## ✅ 2️⃣ OneToOneField (One → One)

### 🔹 Meaning

Ek table ka ek record dusre table ke ek hi record se linked hota hai.

👉 One object → One object

### 🔹 Example Use Case

* User → Profile
* User → Aadhaar
* Employee → ID Card

```python
class User(models.Model):
    username = models.CharField(max_length=100)

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()

```

### ✅ 3️⃣ ManyToManyField (Many ↔ Many)

### 🔹 Meaning
Dono tables ke multiple records ek dusre se linked ho sakte hain.

#### 👉 Many objects ↔ Many objects

### 🔹 Example Use Case

* Students ↔ Courses
* Products ↔ Tags
* Users ↔ Groups

```python
class Course(models.Model):
    name = models.CharField(max_length=100)

class Student(models.Model):
    name = models.CharField(max_length=100)
    courses = models.ManyToManyField(Course)
```

| Field           | Relation Type | Real Example               |
| --------------- | ------------- | -------------------------- |
| ForeignKey      | Many → One    | Many orders → one customer |
| OneToOneField   | One → One     | User → profile             |
| ManyToManyField | Many ↔ Many   | Students ↔ courses         |


# Django me `related_name` Kya Hota Hai?

## ✅ Definition

**related_name** Django model relationship fields (ForeignKey, OneToOneField, ManyToManyField) me use hota hai  
jo **reverse relation ka naam define** karta hai.

Simple words me:

> related_name = parent object se child data access karne ka custom naam

---

## ✅ Pehle Problem Samjho (Without related_name)

### Example

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

- अब एक Author से उसकी books access करनी हों:

author = Author.objects.get(id=1)
author.book_set.all()

- Django by default reverse relation का नाम:
book_set होता है (model name + _set)


### ✅ related_name Use Karne Ke Baad

### ### ForeignKey Example:- 

```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(
        Author,
        on_delete=models.CASCADE,
        related_name="books"
    )

author = Author.objects.get(id=1)
author.books.all()

-- यह book_set की जगह ज्यादा readable है।

### OneToOne Example:- 

class Profile(models.Model):
    user = models.OneToOneField(
        User,
        on_delete=models.CASCADE,
        related_name="profile"
    )

user.profile

### ManyToMany Example:-

class Student(models.Model):
    name = models.CharField(max_length=100)

class Course(models.Model):
    students = models.ManyToManyField(
        Student,
        related_name="courses"
    )

student.courses.all()

```

# Django – `null` vs `blank` Difference

Django models me `null` aur `blank` dono fields ko optional banane ke liye use hote hain —  
lekin dono **different layer** par kaam karte hain.

---

## ✅ `null` Kya Hota Hai?

- `null` database level par kaam karta hai
- Decide karta hai ki DB column me NULL value allowed hai ya nahi
- Agar `null=True` → database me NULL store ho sakta hai

### 🔹 Example

```python
price = models.IntegerField(null=True)
```

## ✅ `blank` Kya Hota Hai?

- blank form/validation level par kaam karta hai
- Decide karta hai ki field form me required hai ya optional
- Agar blank=True → form validation empty value allow karega

```python
    name = models.CharField(max_length=100, blank=True)
```

| Feature            | `null=True`                                      | `blank=True`                          |
| ------------------ | ------------------------------------------------ | ------------------------------------- |
| Level              | Database Level                                   | Form/Validation Level                 |
| Purpose            | Database में `NULL` store करने की अनुमति देता है | Field को optional बनाता है            |
| Affects            | Database Schema                                  | Django Forms, Admin, Model Validation |
| Empty Value        | `NULL`                                           | Empty String (`""`) या empty input    |
| Form Required?     | Required रह सकती है                              | Required नहीं रहती                    |
| Database Required? | NULL allowed                                     | Database constraint नहीं बदलता        |
| Default Value      | `False`                                          | `False`                               |
| Used In            | Models                                           | Models (Form validation के लिए)       |
| Admin Panel        | Direct effect नहीं                               | Field optional दिखाई देती है          |
| Interview Keyword  | **Database**                                     | **Validation/Form**                   |



# Django Model Me Meta Class Ka Use Kya Hai?

## ✅ Meta Class Kya Hoti Hai?

Django model ke andar `class Meta:` ek inner class hoti hai  
jo model ke **behavior aur configuration** ko control karti hai —  
matlab table kaise behave karegi, kaise order hoga, kya constraints honge, etc.

Simple words me:

> Meta class = model ki extra settings ka control panel

---

## ✅ Meta Class Kaha Likhi Jaati Hai?

Meta class model ke andar likhte hain:

```python
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.IntegerField()

    class Meta:
        ordering = ["price"]
```
### 🔹 1️⃣ Table Name Change Karna
```python
class Meta:
    db_table = "my_product_table"
```

### 🔹 2️⃣ Default Ordering Set Karna
```python
class Meta:
    ordering = ["-price"]   # descending
```

### 🔹 3️⃣ Verbose Name (Admin Me Custom Name)
```python
class Meta:
    verbose_name = "Product Item"
    verbose_name_plural = "Product Items"
```

### 🔹 5️⃣ Permissions Add Karna
```python
class Meta:
    permissions = [
        ("can_publish", "Can publish product"),
    ]
```

# Django ORM – `select_related` vs `prefetch_related`

Ye dono Django ORM ke **query optimization tools** hain  
jo related data ko efficiently fetch karne ke liye use hote hain.

Main purpose:

> N+1 query problem ko solve karna & DB queries kam karna

### ✅ select_related Kya Hota Hai?

* SQL JOIN use karta hai
* Related object ko same query me fetch karta hai
* Sirf ForeignKey & OneToOneField relations ke liye
* Single-valued relations

```python
books = Book.objects.select_related("author")

for b in books:
    print(b.author.name)
```

- ✅ Sirf 1 query chalegi
- ✅ Faster for FK / OneToOne

### ✅ prefetch_related Kya Hota Hai?

* Separate queries chalata hai
* Python side pe relation map karta hai
* ManyToMany & reverse ForeignKey ke liye
* Multi-valued relations
```python
authors = Author.objects.prefetch_related("book_set")

for a in authors:
    for b in a.book_set.all():
        print(b.title)
```

| Point     | select_related             | prefetch_related          |
| --------- | -------------------------- | ------------------------- |
| Method    | SQL JOIN                   | Separate queries          |
| Queries   | Single query               | Multiple queries          |
| Best For  | FK, OneToOne               | ManyToMany, reverse FK    |
| Join Type | DB level                   | Python level              |
| Speed     | Faster for single relation | Better for multi relation |

* Single object relation → select_related
* Multiple object relation → prefetch_related

| Feature          | `select_related()`                          | `prefetch_related()`                         |
| ---------------- | ------------------------------------------- | -------------------------------------------- |
| Purpose          | Related object को SQL JOIN से fetch करता है | Related object को अलग query से fetch करता है |
| Queries          | Usually 1 Query                             | 2 या अधिक Queries                            |
| Works With       | `ForeignKey`, `OneToOneField`               | `ManyToManyField`, Reverse FK, FK, O2O       |
| SQL JOIN         | ✅ Yes                                       | ❌ No                                         |
| Data Fetch       | Single query में                            | Multiple queries में                         |
| Performance      | FK/O2O के लिए बेहतर                         | M2M/Reverse Relations के लिए बेहतर           |
| Memory Usage     | कम                                          | थोड़ा ज्यादा                                 |
| Reverse Relation | ❌ Support नहीं                              | ✅ Support                                    |
| ManyToMany       | ❌ Support नहीं                              | ✅ Support                                    |
| Use Case         | Author के साथ Book                          | Book के साथ Tags                             |



# Django ORM – Q Objects Kya Hote Hain?

## ✅ Definition (Short)

**Q objects** Django me complex queries likhne ke liye use hote hain  
jaha multiple conditions ko **OR / AND / NOT** logic ke saath combine karna ho.

Simple line:

> Q objects = complex filter conditions with OR, AND, NOT

---

## ✅ Kyu Use Karte Hain?

Normal filter me conditions default **AND** hoti hain:

```python
Product.objects.filter(category="mobile", price__gt=50000)
```

## ✅ Basic Example (OR Condition)
```python
from django.db.models import Q

products = Product.objects.filter(
    Q(category="mobile") | Q(price__gt=50000)
)
```

## ✅ AND Condition with Q
```python
products = Product.objects.filter(
    Q(category="mobile") & Q(stock__gt=5)
)
```

## ✅ NOT Condition
```python
products = Product.objects.filter(
    ~Q(category="mobile")
)
```

`Q Object Django ORM में complex database queries बनाने के लिए उपयोग किया जाता है। यह OR (|), AND (&) और NOT (~) operations को support करता है, जिससे dynamic और advanced filtering की जा सकती है।
`


# Django ORM – F Expressions Kya Hoti Hain?

## ✅ Definition (Short)

**F expressions** Django me database field ki value ko **direct database level par reference aur update** karne ke liye use hoti hain. 
bina data ko Python me laaye.

`F() Expression का इस्तेमाल database field की current value पर operation करने के लिए किया जाता है, बिना data को Python में लाए।`

`F() Expression Django ORM में database field की existing value को reference करने के लिए उपयोग की जाती है। यह calculations और updates को database level पर perform करती है, जिससे performance बेहतर होती है और race conditions से बचा जा सकता है।`

---

## ✅ Kyu Use Karte Hain?

- Field ki value ko dusri field se compare karna
- Field value increment/decrement karna
- DB level calculation karna
- Race condition avoid karna
- Extra queries bachana

---

## ✅ Example – Increment Field Value

### ❌ Normal Way (2 steps, unsafe)

> Python में data लाकर

```python
p = Product.objects.get(id=1)
p.stock = p.stock + 1
p.save()

- Data DB से fetch हुआ
- Python में increment हुआ
- फिर DB में save हुआ
```

### ✅ F Expression Way (Single DB query)
> without Python में data लाकर increment करने की बजाय सीधे DB में ही increment कर दिया
```python
from django.db.models import F

Product.objects.filter(id=1).update(
    stock = F("stock") + 1
)
```

# Django ORM – filter() vs get() Difference

## ✅ filter() Kya Hai?

- filter() matching records ko return karta hai
- Result **QuerySet** hota hai (list jaisa)
- 0, 1, ya multiple records aa sakte hain
- Kabhi error throw nahi karta (empty queryset de deta hai)

### Example

```python
users = User.objects.filter(is_active=True)
```

## ✅ get() Kya Hai?

- get() exactly ek record return karta hai
- Result single object hota hai (QuerySet nahi)
- Sirf tab use karo jab result unique ho (id, unique field)

```python
user = User.objects.get(id=1)
print(user.username)
```

- get() do cases me error deta hai:
    - Record na mile → DoesNotExist
    - Multiple records mil jaye → MultipleObjectsReturned

#### ✅ Return Type Difference
- filter() → QuerySet
- get()    → Single Model Object

### ✅ Kab Kya Use Kare

- filter():
* Jab multiple records chahiye
* Jab unsure ho result count
* Safe querying

- get():
* Jab exactly 1 record chahiye
* Unique lookup (id/email/slug)

# Django ORM – values() vs values_list() Difference (Simple)

## ✅ values()

- Dictionary queryset return karta hai
- Field name + value pair deta hai

### Example

```python
User.objects.values("id", "username")
```

[{"id": 1, "username": "bittu"}] ===> Dict Formate

## ✅ values_list()

- Tuple queryset return karta hai
- Sirf values deta hai (no field names)

```python
User.objects.values_list("id", "username")
```
[(1, "bittu")] ===> Tuple formate


# Django ORM – exists() vs count() Difference (Short)

## ✅ exists()

- Check karta hai **record present hai ya nahi**
- True / False return karta hai
- Fast hota hai (optimized query)
- Sirf existence check ke liye best

### Example

```python
User.objects.filter(is_active=True).exists()
```
Output ==>> True / False

## ✅ count()

- Total matching records ka number return karta hai
- Integer return karta hai
- Jab exact count chahiye tab use karo

```python
    User.objects.filter(is_active=True).count()
```
Output===>> 5
    

# Django ORM – QuerySet Slicing Kaise Karte Hain?

## ✅ Definition

QuerySet slicing Python list slicing jaisi hoti hai.  
Isse tum limited records fetch kar sakte ho (range/limit).

👉 SQL me ye LIMIT / OFFSET ban jata hai.

---

## ✅ Basic Syntax

```python
QuerySet[start : end]

#First 5 records
users = User.objects.all()[:5]

#Records 5 se 10 tak
users = User.objects.all()[5:10]

#Sirf ek record (by index)
user = User.objects.all()[0]

#Last records (reverse + slice)
users = User.objects.order_by("-id")[:5]
```

# Django ORM – bulk_create Kya Hota Hai?

## ✅ Definition

`bulk_create()` Django ORM ka method hai  
jo **multiple records ko ek hi query me insert** karne ke liye use hota hai.

Simple line:

> bulk_create = ek saath bahut saare objects fast insert karna

---

## ✅ Kyu Use Karte Hain?

- Performance fast hoti hai
- Multiple insert → single DB query
- Loop + save() se better
- Large data insert ke liye best

---

## ❌ Slow Way (Loop Save)

```python
for i in range(1000):
    Product.objects.create(name=f"P{i}")

#Fast Way (bulk_create)
objs = [
    Product(name="P1"),
    Product(name="P2"),
    Product(name="P3"),
]

Product.objects.bulk_create(objs)

#Large Example
products = [
    Product(name=f"Item {i}", price=100)
    for i in range(1000)
]

Product.objects.bulk_create(products)
```

# Django QuerySet – order_by() ka Use

## order_by() kya hota hai?

`order_by()` Django ORM ka method hai jo database se aane wale records ko **sorting (order)** me arrange karta hai.

Iska use QuerySet ke results ko:
- Ascending order (A → Z, 1 → 9)
- Descending order (Z → A, 9 → 1)

---

## Basic Syntax
```python
Model.objects.all().order_by('field_name')
```
---

## Example Model
```python
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.IntegerField()
    created_at = models.DateTimeField()
```
---

## Ascending Order (Default)

> Price low se high
```python
products = Product.objects.all().order_by('price')
```
> ✔ By default ascending order hota hai.

---

## Descending Order

>  Price high se low
```python
products = Product.objects.all().order_by('-price')
```
> ✔ Minus (-) lagane se descending order ho jata hai.

---

## Multiple Fields Order
```python
products = Product.objects.all().order_by('price', 'name')
```
1️⃣ Pehle price ke basis par sort  
2️⃣ Same price wale records → name ke basis par sort

---

## Reverse Current Order
```python
products = Product.objects.all().order_by('price').reverse()

# Kyunki order_by('price') se data lowest-to-highest ho gaya tha, .reverse() lagane ke baad wo Highest-to-Lowest (sabse mehenga product pehle, phir usse sasta) ho jayega.

```
---

## Random Order
```python
from django.db.models.functions import Random

products = Product.objects.order_by('?')
```
⚠ Ye heavy ho sakta hai large tables me — production me avoid karo.

Django me order_by() ke andar jab aap '?' paas karte hain, toh Django database ko bolta hai ki data ko randomly arrange kare.

SQL me ye background me kuch aisa chalta hai: SELECT * FROM product ORDER BY RANDOM(); (ya RAND() database ke hisab se).


# Django – exclude() ka Use

`exclude()` Django ORM ka method hai jo QuerySet se **un records ko hata deta hai** jo given condition match karte hain.

exclude() removes records that match the given condition from the QuerySet.


Matlab:
filter() = jo match kare → include karo  
exclude() = jo match kare → remove karo

---

## Basic Syntax
```python
Model.objects.exclude(field=value)
```
---

### Important Example

## Price 100 wale products ko hata do
```python
products = Product.objects.exclude(price=100)
```
```sql
SQL jaisa:
SELECT * FROM product WHERE price != 100
```
---

## Multiple Conditions
```python
products = Product.objects.exclude(price=100, is_active=False)
```
✔ Dono conditions match karne wale records exclude honge

---

## filter + exclude Together
```python
products = Product.objects.filter(is_active=True).exclude(price__lt=50)
```
✔ Pehle active records lo (Phale Filter krega fir exclude krega)
✔ Fir price < 50 wale hata do

---

## Key Points

- exclude() = NOT condition apply karta hai
- Ye filter() ka opposite behave karta hai
- Chainable hai (filter ke saath use ho sakta hai)
- SQL ke NOT / != jaisa kaam karta hai

---

# Django QuerySet – Lazy Hone Ka Matlab (QuerySet lazy hota hai)

Django me QuerySet **lazy (der se execute hone wala)** hota hai. Matlab jab tum QuerySet likhte ho tab database query turant run nahi hoti.

Django me "QuerySet is lazy" ka matlab hota hai "Alsi (Lazy)"—yani jab tak bilkul zaroorat nahi padti, Django database se data nahi nikalta.

Aap Django me jitni marzi queries likh lo, filter laga lo, order badal lo, Django database ke paas tab tak nahi jayega jab tak aap us data ko sach me use (jaise print karna, loop chalana) nahi karte.

---

## Example
```python
# Line 1: Sirf query ban rahi hai, database me KUCh NHI HUA.
q1 = Product.objects.all()

# Line 2: Query me filter jud gaya, abhi bhi database me KUCH NHI HUA.
q2 = q1.filter(category="Electronics")

# Line 3: Sorting jud gayi, abhi bhi database untouched hai.
q3 = q2.order_by('-price')

### Agar aap upar di gayi 3 lines ko execute karenge, toh database par 0 (shunya) load padega. Django ne sirf ek SQL query taiyar ki hai, usko database par chalaya (hit) nahi hai.
```

## Query Kab Execute Hoti Hai?

- loop chalao
  for p in qs:
      print(p.name)

- list() me convert karo
  data = list(qs)
  print(data)

- slicing karo
  qs[:5]

- count(), exists(), first() call karo
  qs.count()

- template me use karo

---

## Lazy Hone Ka Fayda

✔ Performance better hoti hai  
✔ Multiple filters chain kar sakte ho before execution  
✔ Unnecessary DB calls avoid hoti hain  
✔ Query optimize hoti hai before run

---

## Important Note

QuerySet chain hota rahega:
```python
qs = Product.objects.filter(active=True).exclude(price=0).order_by('name')
```

✔ Still DB hit nahi hogi  
✔ Final evaluation par hi query run hogi

---

## Interview Line

Django QuerySets are lazy — they don’t hit the database until the data is actually needed ya data ko print nhi kroge.


# Django – N+1 Query Problem

## N+1 Query Problem kya hoti hai?

N+1 query problem tab hoti hai jab:
- Pehle 1 query chalti hai main records lane ke liye
- Fir har record ke liye alag-alag extra query chalti hai related data lane ke liye

Matlab:
1 (main query) + N (har row ke liye query) = N+1 queries

Ye performance issue create karta hai.

---

## Simple Example

models:
```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```
---

## Problem Case

books = Book.objects.all()

for b in books:
    print(b.author.name)

Queries:
- 1 query → books fetch
- N queries → har book ka author fetch

Agar 50 books hain → total 51 queries ❌

---

## Solution – select_related()
```python
books = Book.objects.select_related('author')
```
✔ Join laga kar related data ek hi query me le aata hai  
✔ ForeignKey / OneToOne ke liye best

---

## ManyToMany / Reverse FK Case – prefetch_related()
```python
books = Book.objects.prefetch_related('tags')
```
✔ Separate query chalata hai but optimized batching ke saath  
✔ ManyToMany / reverse relations ke liye use hota hai

---

## Key Points

- N+1 = performance killer
- Loop me related field access → red flag
- FK / O2O → select_related()
- M2M / reverse → prefetch_related()
- Django ORM optimization ka common interview topic

---

# Django – Function Based View (FBV) vs Class Based View (CBV)

## Function Based View (FBV)

Function Based View ek simple Python function hoti hai jo request leti hai aur response return karti hai.

### Structure
```python
def my_view(request):
    return HttpResponse("Hello")
```
### Features
- Simple aur easy to understand
- Beginners ke liye best
- Full control flow manually likhna padta hai
- Large logic me code repetitive ho sakta hai
- Decorators use karte hain (login_required etc.)

### Use When
- Logic simple ho
- Custom flow chahiye
- Quick views likhni ho

---

## Class Based View (CBV)

Class Based View ek Python class hoti hai jisme methods (get, post, etc.) define hote hain request handle karne ke liye.

### Structure
```python
class MyView(View):
    def get(self, request):
        return HttpResponse("Hello")
```

### Features
- Object Oriented approach
- Code reusable hota hai
- Built-in generic views milti hain (ListView, CreateView, UpdateView)
- Less code for CRUD operations
- Mixins use karke features add kar sakte ho
- Thoda complex lagta hai beginners ko

### Use When
- CRUD operations ho
- Reusability chahiye
- Large project structure ho
- Same logic multiple views me use karna ho

---

## Key Differences

| Point | FBV | CBV |
|-------|------|------|
Simple | ✅ | ❌ (thoda complex) |
Learning | Easy | Medium |
Reusability | Low | High |
Code Size | Zyada | Kam (generic views) |
OOP | ❌ | ✅ |
Best For | Small logic | Large apps |

---

## Interview Line

FBV simple functions par based hoti hain, jabki CBV class aur OOP concepts use karti hain for better reuse and structure.


# Django – render() vs redirect() Difference

## render() kya karta hai?

`render()` template ko load karta hai aur usko data ke saath **direct response** me browser ko bhej deta hai.

Browser me URL same rehta hai — change nahi hota.

### Example

return render(request, "home.html", {"name": "Bittu"})

### Points

- Template show karne ke liye use hota hai
- Context data pass kar sakte ho
- Same request me response milta hai
- URL change nahi hota
- Extra HTTP request nahi hoti

---

## redirect() kya karta hai?

`redirect()` browser ko bolta hai ki **dusre URL par jao**.  
Ye new request trigger karta hai.

### Example

return redirect("home")  
return redirect("/home/")

### Points

- Dusre view / URL par bhejta hai
- URL change ho jata hai
- New HTTP request hoti hai
- Mostly form submit ke baad use hota hai
- POST → Redirect → GET pattern follow karta hai

---

## Main Difference

| Point | render() | redirect() |
|--------|------------|-------------|
Kaam | Template show | URL change |
Request | Same request | New request |
URL | Same rehta | Change hota |
Use Case | Page display | Page change / after POST |
Speed | Faster | Thoda slower |

---

# Django – request Object Me Kya Kya Hota Hai?

## request object kya hota hai?

Django me `request` object har view ko automatically milta hai.  
Isme client (browser) se aayi hui saari information hoti hai:

- user data
- form data
- headers
- method
- files
- session
- cookies
- URL info

---

## Important request Attributes

### request.method
Batata hai request type kya hai

---

### request.GET
GET parameters (query string data)

request.GET.get("name")

URL:
?page=2&name=bittu

---

### request.POST
Form POST data

request.POST.get("email")

✔ Form submissions ke liye use hota hai

---

### request.FILES
Uploaded files data

request.FILES["file"]

✔ File upload handle karne ke liye

---

### request.user
Current logged-in user object

request.user.username

✔ Authentication ke saath use hota hai

---

### request.session
Session data store / get

request.session["cart"] = 5

✔ Per-user data store

---

### request.COOKIES
Browser cookies access

request.COOKIES.get("theme")

---

### request.path
Current URL path

request.path  
/products/list/

---

### request.headers
Request headers

request.headers.get("User-Agent")

---

### request.body
Raw request body (mostly APIs me use)

request.body

---

## Key Points

- request = client request ka full container
- Form, files, user, session sab yahi se milta hai
- Har Django view ka first parameter hota hai
- API aur form handling me very important

---

## Interview Line

Django request object contains all client request data like method, GET/POST data, user, files, headers, session, and cookies.


# Django – JsonResponse Kab Use Karte Hain (JsonResponse kya hota hai)?

`JsonResponse` Django ka special response class hai jo data ko **JSON format** me browser/client ko return karta hai.

Ye mainly APIs aur AJAX requests me use hota hai.

---

## JsonResponse Kab Use Karte Hain?

### ✅ Jab API bana rahe ho
Frontend / mobile app ko JSON data chahiye hota hai

### ✅ AJAX requests me
Page reload bina data bhejna ho

### ✅ JavaScript frontend (React / Vue / Angular) ke saath
Backend → JSON return karta hai

### ✅ API endpoints me
REST type responses

---

## Important Example
```python
from django.http import JsonResponse

def data_view(request):
    data = {
        "name": "Bittu",
        "marks": 90
    }
    return JsonResponse(data)
```
Response:
{
  "name": "Bittu",
  "marks": 90
}

---

## List Return Karna Ho To

return JsonResponse(data_list, safe=False)

✔ By default dict allowed hota hai  
✔ List bhejne ke liye safe=False dena padta hai

---

## JsonResponse vs render()

render() → HTML page return  
JsonResponse → JSON data return

---

## Key Points

- Content-Type = application/json
- Dict → direct allowed
- List → safe=False required
- API + AJAX + JS frontend me common use
- DRF ke bina bhi basic API bana sakte ho

---

# Django – request.GET vs request.POST Difference

## request.GET kya hai?

`request.GET` URL ke query parameters ka data hold karta hai.  
Ye tab use hota hai jab form ya request GET method se bheji gayi ho.

### Example URL

/products/?page=2&name=bittu

### Access

request.GET.get("page")

### Features

- Data URL me visible hota hai
- Small data ke liye use
- Bookmark/share ho sakta hai
- Less secure
- Mostly search / filter forms

---

## request.POST kya hai?

`request.POST` form ke POST method se bheja gaya data hold karta hai.

### Example

<form method="post">

### Access

request.POST.get("email")

### Features

- Data URL me visible nahi hota
- Body me send hota hai
- More secure than GET
- Large data bhej sakte ho
- Form submit / create/update operations

---

## Important Example

if request.method == "POST":
    name = request.POST.get("name")
else:
    name = request.GET.get("name")

---

## Main Differences

| Point | request.GET | request.POST |
|--------|--------------|---------------|
Method | GET | POST |
Data Location | URL | Request body |
Visibility | Visible in URL | Hidden from URL |
Size Limit | Limited | Large allowed |
Security | Low | Better |
Use Case | Search, filter | Form submit, save data |

---

## Key Points

- Dono dictionary-like objects hain
- .get() use karte hain safe access ke liye
- request.method check karna best practice
- CSRF protection POST me required hoti hai

---

## Interview Line

request.GET reads data from URL query parameters, while request.POST reads data sent in the request body via POST method.
