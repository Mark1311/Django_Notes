# Django – Template Inheritance Kya Hota Hai?

Template inheritance Django ka feature hai jisme:
Ek **base template** banate hain (common layout)  
Aur baaki templates usko **extend** karte hain.

> बार-बार एक ही HTML कोड (जैसे Navbar, Sidebar, या Footer) को हर पेज पर अलग से लिखने के बजाय, आप एक "Main" या "Base" टेम्पलेट बना लेते हैं और बाकी के पेजेस उसी मेन टेम्पलेट को कॉपी (inherit) कर लेते हैं।

Matlab:
Common design ek jagah → reuse everywhere

---

## Kyu Use Karte Hain?

- Repeated HTML code avoid
- Same header/footer/navbar reuse
- Layout consistent rehta hai
- Maintenance easy hoti hai

---

## Base Template Concept

Base template me common cheezein hoti hain:

- header
- footer
- navbar
- CSS/JS links
- layout structure

Aur beech me dynamic area define karte hain using blocks.

---

## Important Example

### base.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>{% block title %}My Website{% endblock %}</title>
</head>
<body>

    <nav>
        <a href="/">Home</a> | <a href="/about/">About</a>
    </nav>

    <hr>

    <div class="container">
        {% block content %}
        {% endblock %}
    </div>

    <hr>

    <footer>
        <p>© 2026 My Django Website</p>
    </footer>

</body>
</html>
```
---

### child.html
```html
{% extends 'base.html' %}

{% block title %}Home - Welcome to my site{% endblock %}

{% block content %}
    <h1>Welcome to the Home Page!</h1>
    <p>Yeh is page ka unique content hai jo sirf Home par dikhega.</p>
{% endblock %}
```
✔ child template base ko extend karta hai  
✔ block content ko replace karta hai

---

## Important Tags

### extends
Parent template ko inherit karta hai
```html
{% extends "base.html" %}
```
### block
Replaceable section define karta hai
```html
{% block content %} {% endblock %}
```
---

## Rules

- extends line template ki first line honi chahiye
- Block names same hone chahiye base aur child me
- Multiple blocks ho sakte hain

---

## Key Points

- DRY principle follow karta hai (Don’t Repeat Yourself)
- Large projects me must-use pattern
- Layout control centralized hota hai
- Code clean aur reusable

---

# Django Templates – block & extends Ka Use

## extends ka Use

`extends` tag ka use child template ko **base template inherit** karwane ke liye hota hai.

Matlab:
Child template → parent (base.html) ka layout reuse karega.

### Example
```html
{% extends "base.html" %}
```
✔ Hamesha template ki first line me likhte hain  
✔ Base layout load karta hai

---

## block ka Use

`block` tag base template me **replaceable section** define karta hai.  
Child template is block ko override (replace) karta hai.

### Base Template
```html
{% block content %}{% endblock %}
```
---

### Child Template
```html
{% block content %}
<h1>Dashboard Page</h1>
{% endblock %}
```
✔ Child template yaha apna content inject karta hai

---

## Simple Flow

1️⃣ base.html me blocks define  
2️⃣ child.html me extends use  
3️⃣ child same block name override karta hai

---

## Important Rules

- extends → first line hona chahiye
- block names same hone chahiye
- Multiple blocks allowed (title, content, scripts, etc.)
- Nested inheritance bhi possible

---

## Common Block Names (Best Practice)

- block title
- block content
- block scripts
- block styles

---

## Key Points

- extends = template inherit karna
- block = dynamic replaceable area banana
- Layout reuse + clean structure
- DRY principle follow

---

# Django – Template Filters Kya Hote Hain?

Template filters Django templates me use hote hain data ko **format / modify** karne ke liye — directly template ke andar.

`Django mein Template Filters (टैम्प्लेट फिल्टर्स) का इस्तेमाल HTML टेम्पलेट के अंदर दिखने वाले Variables (डाटा) के लुक, फॉर्मेट या वैल्यू को बदलने (modify करने) के लिए किया जाता है।  `

> जब आप डेटाबेस से कोई डाटा (जैसे नाम, तारीख, या नंबर) व्यू (View) के ज़रिए टेम्पलेट में भेजते हैं, तो कभी-कभी आपको उसे यूजर को दिखाने से पहले थोड़ा साफ-सुथरा या फॉर्मेट करना पड़ता है। बिना views.py में बदलाव किए, सीधे HTML में ही यह काम करने के लिए Django हमें Filters देता है।

---

## Syntax

{{ value | filter_name }}

✔ Pipe ( | ) operator use hota hai

---

## Important Examples (Most Used)

### Uppercase

{{ name|upper }}

---

### Lowercase

{{ name|lower }}

---

### Length

{{ items|length }}

---

### Default Value

{{ username|default:"Guest" }}

✔ Agar value empty ho to default show karega

---

### Date Format

{{ created_at|date:"Y-m-d" }}

---

### Safe (HTML render karne ke liye)

{{ html_data|safe }}

✔ HTML escape nahi karega

---

## Filter With Argument

{{ text|truncatewords:5 }}

✔ First 5 words hi show karega

---

## Multiple Filters Chain

{{ name|lower|capfirst }}

✔ Pehle lower → fir first letter capital

---

## Key Points

- Template level data formatting
- Pipe operator use hota hai
- Clean templates without logic clutter
- Built-in + custom filters possible

---

# Django – Custom Template Filter Kaise Banate Hain?

Jab built-in template filters enough na ho, tab hum apna khud ka filter banate hain  
taaki template me custom data formatting kar sakein.

> Custom template filters are created using a templatetags module and registered with @register.filter to extend Django template functionality.

---

## Steps to Create Custom Template Filter

### Step 1 – app ke andar folder banao
```text
your_app/
    templatetags/
        __init__.py
        custom_filters.py

✔ templatetags folder ka naam exact yehi hona chahiye  
✔ __init__.py required hai
```
---

## Step 2 – Filter Function Likho
```python
custom_filters.py

from django import template

register = template.Library()

@register.filter
def double(value):
    return value * 2
```

✔ Function ka 1st argument → template value hota hai

---

## Step 3 – Template Me Load Karo

Template file me:

{% load custom_filters %}

---

## Step 4 – Use in Template

{{ price|double }}

Agar price = 50 → output = 100

---

## Filter With Argument
```python
@register.filter
def add_num(value, num):
    return value + num

Template:

{{ price|add_num:10 }}
```
---

## Important Rules

- templatetags folder app ke andar hi hona chahiye
- Server restart karna padta hai new filters ke baad
- Function simple aur side-effect free hona chahiye
- Templates me heavy logic avoid karo

---
## Key Points

- @register.filter decorator use hota hai
- Template formatting ke liye best
- Reusable filters bana sakte ho
- Arguments support karta hai

---

# Django – Static Files Kaise Load Karte Hain?

## Static Files kya hoti hain?

Static files = wo files jo fixed hoti hain:

- CSS
- JS
- Images
- Fonts

Ye templates me use hoti hain design aur UI ke liye.
> Static files are loaded in Django templates using {% load static %} and the {% static %} template tag to reference CSS, JS, and image files.

---

## Step 1 – settings.py Me Config
```python
INSTALLED_APPS me ensure karo:

'django.contrib.staticfiles'
```
---

## Step 2 – Static Folder Structure
```text
your_app/
    static/
        your_app/
            style.css
            logo.png

✔ Best practice: static/app_name/…
```
---

## Step 3 – Template Me Static Load Karo
```python
Template ki top par:

{% load static %}
```
---

## Step 4 – Static File Use Karo

CSS:
```css
<link rel="stylesheet" href="{% static 'your_app/style.css' %}">

Image:

<img src="{% static 'your_app/logo.png' %}">
```
JS:
```js
<script src="{% static 'your_app/app.js' %}"></script>
```
---

## Important Settings (Basic)

STATIC_URL = "/static/"

---

## Production Me

collectstatic command use hota hai:
```python
python manage.py collectstatic
```
✔ Saari static files ek jagah collect ho jati hain

---

## Key Points

- {% load static %} required hai template me
- static tag path resolve karta hai
- Static = CSS, JS, images
- App-wise folder structure recommended
- collectstatic production me use hota hai

---

# Django – Forms vs HTML Forms vs ModelForm Difference

## 1️⃣ HTML Forms

Ye normal frontend forms hote hain jo sirf HTML me likhe jate hain.

### Example

<form method="post">
  <input type="text" name="name">
</form>

### Features

- Pure HTML based
- Validation manually karni padti hai
- Backend me data manually handle
- No Django integration by default
- Simple cases me useful

---

## 2️⃣ Django Forms (forms.Form)

Django ka built-in form system — Python class ke through form define karte hain.

`Yeh Django ka ek aisa tool hai jo website par HTML forms banane, user ke daale gaye data ko check (validate) karne, aur use securely handle karne ka kaam aasan banata hai.`

`Jab aap kisi website par Form bharte hain, toh piche ka saara sir-dard (ki user ne sahi email dala ya nahi, koi blank toh nahi chhod diya, ya data safe hai ya nahi) Django Form akele sambhal leta hai. Aapko manually lambi-lambi coding nahi karni padti.`
### Example
```python
# Model.py
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
```
```python
# view.py
from django.shortcuts import render
from .forms import ContactForm

def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid(): # Yeh check karta hai ki data sahi hai ya nahi
            # Yahan aap data ke sath kuch bhi kar sakte hain (jaise email bhejna)
            print(form.cleaned_data['naam']) 
    else:
        form = ContactForm() # Khali form dikhane ke liye
        
    return render(request, 'contact.html', {'form': form})
```
```html
#template..html
<form method="POST">
    {% csrf_token %} {{ form.as_p }}  <button type="submit">Submit</button>
</form>
```

### Features

- Server-side validation built-in
- Cleaned data milta hai
- Form rendering easy
- Security better (CSRF, validation)
- Database se direct linked nahi

### Use When

- Custom forms
- DB model directly use nahi karna
- Validation strong chahiye

---

## 3️⃣ ModelForm

ModelForm = Django Form + Database Model auto link. Model ke fields se form automatically ban jata hai.

`Pehle ek choti si real-life line: Django Form me aapko batana padta hai ki "Mujhe ek Name ka box chahiye, ek Email ka box chahiye." Lekin ModelForm itna smart hai ki woh aapke Database ki Table ko dekh kar apne aap samajh jata hai ki use kaun-kaun se boxes banane hain.`


### Example
```python
# model.py
> Yahan aapne Django ko bataya ki mujhe database me student ka kya-kya data save karna hai.

from django.db import models

class Student(models.Model):
    naam = models.CharField(max_length=100)
    roll_number = models.IntegerField()
    email = models.EmailField()
    bio = models.TextField()

> Ab agar aap normal Form use karte, toh aapko forms.py me ja kar ye saari fields dobara likhni padtin (naam, roll_number, email, bio). Double mehnat!

# Form.py
> Aap Django ko bologe: "Bhai, jo mera Student wala model hai na, usko dekh kar apne aap ek form taiyar kar de."

from django import forms
from .models import Student # Apne model ko import kiya

class StudentForm(forms.ModelForm):
    class Meta:
        model = Student  # Django ko bataya ki is model ka form banana hai
        fields = '__all__' # Aur bataya ki saari fields (`__all__`) ka form bana do

> Bas itna sa code! Django ne apne aap samajh liya ki use ek text box naam ke liye chahiye, ek number box roll_number ke liye, aur ek bada text area bio ke liye.

# Form.py

> Normal form me aapko data nikal kar khud save karna padta hai, lekin ModelForm me sirf ek command likhni hoti hai: .save() aur data seedhe database me permanent save!

from django.shortcuts import render, redirect
from .forms import StudentForm

def register_student(request):
    if request.method == 'POST':
        form = StudentForm(request.POST) # User ka bhara hua data form me dala
        if form.is_valid():
            form.save() # MAGIC LINE! Data apne aap database me save ho gaya.
            return redirect('success_page')
    else:
        form = StudentForm() # Khali form dikhane ke liye
        
    return render(request, 'register.html', {'form': form})
```
```html
# template.html
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }} <button type="submit">Register Student</button>
</form>
```

### Features

- Model fields se auto form create
- Save method built-in
- Less code for CRUD
- Validation + DB mapping
- Fast development

### Use When

- Model data create/update karna ho
- CRUD operations
- Admin type forms

---

## Main Difference Table

| Feature / Khaasiyat | HTML Form (Akela) | Django Form (Standard) | Django ModelForm |
| :--- | :--- | :--- | :--- |
| **Yeh Asal Me Kya Hai?** | Sirf ek **Frontend** ka dhabba (structure) hai jo browser me dikhta hai. | Ek **Backend Python Class** hai jo HTML generate aur validate karti hai. | Ek special Django Form hai jo direct **Database Table (Model)** se juda hota hai. |
| **Code Kahan Likha Jata Hai?** | `.html` file ke andar. | `.py` (python) file ke andar. | `.py` (python) file ke andar. |
| **Fields Kaun Banata Hai?** | Aapko manually `<input>` tags likhne padte hain. | Aapko Python me `forms.CharField()` jaise fields likhni padti hain. | Yeh **Automatic** aapke Database Model se fields utha leta hai (Aapko likhna nahi padta). |
| **Data Validation (Checking)** | **Zero.** Agar user email ki jagah apna naam likh de, toh yeh nahi rok paega. | **Strong.** Inbuilt validation hoti hai, galat data par turant error dikha deta hai. | **Strong + Automatic.** Jo validation aapke database me lagi hai (jaise max_length), yeh use apne aap check kar leta hai. |
| **Security (Hackers se bachao)** | Bilkul nahi. Aapko khud security codes likhne padenge. | **Inbuilt Security.** CSRF attacks se protection milti hai. | **Inbuilt Security.** Isme bhi full protection milti hai. |
| **Database Me Data Save Karna** | Iska database se koi lena-dena nahi hota. Bohot lamba backend code likhna padta hai. | Data validate hone ke baad, aapko manually use database me bhejni padti hai. | Sabse aasan! Bas **`form.save()`** likho aur data seedhe database table me save. |
| **Sabse Best Use Case?** | Jab aap bina kisi framework ke ek simple static web page bana rahe hon. | Jab aapko aisa data chahiye jo **database me save nahi karna** (e.g., Search Bar ya Contact Email Form). | Jab aapko data direct **database me save/insert karna ho** (e.g., Sign Up, Blog Post, Product Add karna). |

---

# Django – Form Validation Kaise Hoti Hai?

## Form Validation kya hoti hai?

Jab bhi koi user aapki website par kisi form me data bharta hai, toh backend (Django) me save karne se pehle yeh check karna bohot zaroori hai ki jo data aaya hai woh sahi format me hai ya nahi. Is cheez ko check karne ke process ko Django me Validation kehte hain.

- sahi format me ho
- required fields filled ho
- rules follow kare

Django me validation **automatic + custom** dono tarike se hoti hai.

---

## Validation Flow (Django Forms)

1️⃣ Form submit hota hai  
2️⃣ Form class data receive karti hai  
3️⃣ form.is_valid() call hota hai  
4️⃣ Django built-in validators run karta hai  
5️⃣ Cleaned data milta hai

---

## Basic Example
```python
form = ContactForm(request.POST)

if form.is_valid():
    data = form.cleaned_data

✔ is_valid() → validation trigger karta hai  
✔ cleaned_data → validated data deta hai
```
---

## Built-in Field Validation
```python
from django import forms

class MyForm(forms.Form):
    email = forms.EmailField()
    age = forms.IntegerField(min_value=18)
```

✔ Type check  
✔ Required check  
✔ Min/Max rules

---

## Custom Field Validation

Specific field validate karne ke liye:
```python
def clean_email(self):
    data = self.cleaned_data['email']
    if not data.endswith('@mycompany.com'):
        raise ValidationError("Sirf company ka official email chalega!")
    return data
```

✔ Method name → clean_<fieldname>

---

## Form Level Validation

Multiple fields together validate karne ke liye:
```python
def clean(self):
    data = super().clean()
    p1 = data.get("password")
    p2 = data.get("confirm_password")

    if p1 != p2:
        raise forms.ValidationError("Passwords not match")
```
✔ Cross-field validation

---

## ModelForm Validation
```python
#mode.py
from django.db import models

class Product(models.Model):
    title = models.CharField(max_value=100, unique=True) # Title unique hona chahiye
    price = models.DecimalField(max_digits=6, decimal_places=2)
    stock = models.IntegerField()

#form.py
from django import forms
from .models import Product

class ProductForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = ['title', 'price', 'stock']
```
Yahan aapko koi validation code likhne ki zarurat nahi hai. Django automatic ye cheezein check karega:

- Kya title khali toh nahi hai? (Required check)

- Kya title database me pehle se exist toh nahi karta? (Unique check)

- Kya price aur stock me sahi me numbers bhare gaye hain?
---

## Key Points

- form.is_valid() mandatory
- cleaned_data only after valid
- Built-in + custom validation possible
- clean_field() → field level
- clean() → form level

---

# Django – CSRF Token Kya Hai Aur Kyu Use Hota Hai?

CSRF = Cross-Site Request Forgery

Ye ek security attack hai jisme attacker user ke browser se bina permission
fake request bhej deta hai — jaise form submit, password change, payment action.

---

## CSRF Token kya hai?

CSRF token ek **unique secret value** hoti hai jo Django har form/request ke saath attach karta hai
taaki verify ho sake ki request same trusted site se aayi hai.

Server check karta hai:
✔ Token valid hai?  
✔ Token match karta hai?  
→ tabhi request accept hoti hai

---

## Django Me CSRF Kaise Use Karte Hain?

### Template Form Me

<form method="post">
    {% csrf_token %}
</form>

✔ Ye hidden input field add karta hai token ke saath  
✔ POST form me required hai

---

## Validation Flow

1️⃣ User page open karta hai → CSRF token generate  
2️⃣ Form me token embed hota hai  
3️⃣ Form submit hota hai  
4️⃣ Server token verify karta hai  
5️⃣ Match → request allowed  
❌ Not match → request blocked (403 error)

---

## Kab Required Hota Hai?

- POST requests
- PUT / DELETE (unsafe methods)
- Form submissions
- Sensitive actions

GET requests me usually required nahi hota.

---

## Agar CSRF Token Na Ho

Error milega:

403 Forbidden – CSRF verification failed

---

## Key Points

- Security against fake form submissions
- Django me by default enabled
- {% csrf_token %} template tag use hota hai
- Mostly POST forms me mandatory
- Middleware handle karta hai

---

# Django – urls.py Ka Role

## urls.py kya hota hai?

`urls.py` Django project ka **URL router** hota hai.  
Ye decide karta hai ki kaunsa URL hit hone par kaunsa view function/class run hoga.

Matlab:
URL → View mapping yahi define hoti hai.

---

## Simple Meaning

Browser me jo path likhte ho → urls.py usko match karta hai  
Aur correct view ko request forward karta hai.

---

## Basic Example
```python
from django.urls import path
from . import views

urlpatterns = [
    path("home/", views.home),
]
```
✔ /home/ URL → home view call hogi

---

## Project Level vs App Level urls.py

### Project urls.py
- Main entry router
- Multiple apps ke urls include karta hai

> Jab aap django-admin startproject myproject command chala kar naya project banate ho, toh yeh file automatic ban jati hai.

Yeh Kya Hai: Yeh aapki poori website ka Main Controller hai. Kisi bhi user ki request sabse pehle isi file ke paas aati hai.

Iska Kaam: Iska kaam har request ko uske sahi App ki taraf bhej dena (forward karna) hota hai. Isme hum views ke code ko direct link karne ke bajaye, apps ki urls.py ko include() karte hain.

Example:
```python
from django.contrib import admin
from django.urls import path, include # include ko import karna zaroori hai

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')), # Blog app ki taraf rasta mod diya
    path('shop/', include('shop.urls')), # Shop app ki taraf rasta mod diya
]
```
---

### App urls.py

> Jab aap naya app banate ho (python manage.py startapp blog), toh Django isme urls.py file khud se bana kar nahi deta. Aapko yeh file khud manually create karni padti hai.

Yeh Kya Hai: Yeh kisi ek specific feature ya module (jaise blog, ecommerce shop, ya user profile) ke andar ka local raasta taiyar karti hai.

Iska Kaam: Yeh un specific requests ko direct Views (Python functions) se connect karti hai jo us app ka kaam sambhalte hain.
```python
from django.urls import path
from . import views # Apne app ke views ko import kiya

urlpatterns = [
    path('', views.blog_home, name='home'),       # Asli URL: website.com/blog/
    path('post/', views.blog_post, name='post'), # Asli URL: website.com/blog/post/
]
```

## Key Points

- URL → View mapping
- Routing system of Django
- Project + app level dono ho sakta hai
- Dynamic parameters support
- Named URLs best practice

# Django – path() vs re_path() Difference

## path() kya hai?

`path()` simple aur readable URL patterns define karne ke liye use hota hai.  
Ye Django ka modern aur recommended way hai URL routing ka.


## Example
```python
from django.urls import path
from . import views

urlpatterns = [
    # Simple URL
    path('about/', views.about_page),
    
    # URL jisme dynamic ID chahiye (Path Converter ke sath)
    path('post/<int:post_id>/', views.post_detail), 
]

# Yahan <int:post_id> ka matlab hai ki URL me post/ ke baad sirf ek Integer (number) hi aana chahiye, jaise post/5/.
```

## Built-in Converters

- <int:id>
- <str:name>
- <slug:slug>
- <uuid:id>
- <path:value>

---

## re_path() kya hai?

`re_path()` regular expressions (regex) based URL matching ke liye use hota hai. Jab complex pattern matching chahiye ho tab use karte hain.

## Example
```python
re_path(r"^product/(?P<id>[0-9]+)/$", views.product_detail)

# Yeh URL sirf tabhi chalega jab username rules ko follow karega
re_path(r'^(?P<username>[a-z0-9]{4,12})/$', views.user_profile),
```
✔ Full regex power  
✔ Complex patterns possible  
❌ Syntax complex

---

## Kab Use Kare?

### path()
- Normal URLs
- Clean routes
- 95% cases
- Recommended approach

### re_path()
- Complex matching
- Special patterns
- Legacy regex routes
- Jab converters enough na ho

---

## Main Differences

| Point | path() | re_path() |
|--------|---------|------------|
Syntax | Simple | Regex based |
Readability | High | Low |
Complex Patterns | Limited | Powerful |
Converters | Built-in | Manual regex |
Use | Default choice | Special cases |

## Key Points

- path() → simple & modern
- re_path() → regex support
- New projects me path() prefer karo
- re_path() = advanced routing


# Django – URL Parameters Kaise Lete Hain?

## URL Parameters kya hote hain?

URL parameters wo dynamic values hoti hain jo URL se pass hoti hain
aur view function/class me receive hoti hain.

Ye mostly detail pages, edit pages, filtering, etc. me use hoti hain.

---

## Step 1 – urls.py Me Parameter Define
```python
from django.urls import path
from . import views

urlpatterns = [
    path("product/<int:id>/", views.product_detail),
]
```

✔ <int:id> → dynamic parameter
✔ Type converter + variable name

---

## Step 2 – View Me Receive
```python
def product_detail(request, id):
    return HttpResponse(id)
```
✔ Same naam ka argument view me lena hota hai

---

## Multiple Parameters
```python
path("order/<int:oid>/item/<int:iid>/", views.item_view)

def item_view(request, oid, iid):
    pass
```
---

## Common Converters

- <int:id>
- <str:name>
- <slug:slug>
- <uuid:id>
- <path:value>

---

## Query Parameters (GET se)

URL:
 /products/?page=2

View:

page = request.GET.get("page")

✔ Ye path parameter nahi — query parameter hai

---

# Django – URL Namespace Kya Hota Hai?

## Namespace kya hota hai?

Namespace URLs ko **group / isolate** karne ka tarika hai. taaki same name wale URLs different apps me conflict na karein.

Namespace (या URL Namespacing) Django ka ek aisa feature hai jo aapke alag-alag apps ke beech ke URL Names ka collision (takraav) rokta hai.

Simple shabdon me kahein toh, yeh bilkul waisa hi hai jaise do alag-alag gharon me do bacchon ka naam "Rahul" ho sakta hai. Agar aap sirf "Rahul" bologe toh confusion ho jayega, lekin agar aap bologe "Sharma ji ka Rahul" ya "Verma ji ka Rahul", toh sab samajh jayenge.

Matlab:
Same URL name → different apps → no clash

---

## Problem Without Namespace

2 apps:
- shop → name="list"
- blog → name="list"

Template me:
{% url "list" %}

❌ Conflict / confusion

---

## Namespace Solution

### App urls.py

app_name = "shop"

urlpatterns = [
    path("products/", views.list_view, name="list"),
]

---

## Project urls.py

path("shop/", include("shop.urls"))

---

## Template Me Use

{% url "shop:list" %}

✔ namespace:urlname format

---

## Key Points

- app_name define karna padta hai
- include() ke through use hota hai
- Same URL names safe ho jate hain
- Large projects me must-use

# Django – Django Admin Kya Hai?

Django Admin ek built-in **auto-generated backend panel** hai  
jisse hum database models ka data manage kar sakte hain — bina alag UI banaye.

Isse hum:

- records add
- update
- delete
- search
- filter

## Django Admin Features

- Model based auto CRUD panel
- Authentication required
- Superuser access
- Search & filters support
- List display customize kar sakte ho
- Forms auto generate hoti hain

## Admin URL

/admin/

✔ Project create karte hi available hota hai  
✔ Superuser se login hota hai

---

## Superuser Create
```cmd
python manage.py createsuperuser
```

## Key Points

- Built-in backend tool
- Fast data management
- No frontend coding needed
- Dev & admin users ke liye best

---

# Django – Model Admin Register Kaise Karte Hain?

## Model admin me register kyu karte hain?

Jab tak model admin me register nahi hota,  
wo Django admin panel me visible nahi hota.

Register karne ke baad hi admin UI me manage kar sakte ho.

---

## Step – admin.py Me Register
```python
from django.contrib import admin
from .models import Product

admin.site.register(Product)
```

✔ Ab Product model admin panel me dikhega

---

## Custom ModelAdmin Class (Important)
```python
from django.contrib import admin
from .models import Product

class ProductAdmin(admin.ModelAdmin):
    list_display = ("id", "name", "price")

admin.site.register(Product, ProductAdmin)
```

✔ list_display → columns control karta hai

## Decorator Style (Shortcut)
```python
@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    list_display = ("name", "price")
```
✔ Same kaam — cleaner syntax

## Key Points

- admin.py file me register hota hai
- admin.site.register(Model)
- Custom ModelAdmin possible
- list_display, search_fields, filters add kar sakte ho

---

# Django Admin – list_display Kya Hota Hai?

## list_display kya hai?

`list_display` Django admin me use hota hai decide karne ke liye ki, **model list page par kaun-kaun se columns dikhaye jayenge**.

Simple shabdon me kahein toh, jab aap Django Admin Panel me kisi Database Model (table) par click karte ho, toh wahan aapko apne saare data ki jo List (Table format me) dikhti hai, usme kaun-kaun se columns dikhne chahiye, yeh tay karne ka kaam list_display karta hai.

---

## Important Example
```python
# models.py
class Student(models.Model):
    naam = models.CharField(max_length=100)
    roll_no = models.IntegerField()
    email = models.EmailField()
    course = models.CharField(max_length=50)

    def __str__(self):
        return self.naam

# admin.py
from django.contrib import admin
from .models import Student

class StudentAdmin(admin.ModelAdmin):
    # Jo jo fields aapko bahar table ke columns me chahiye, unke naam yahan likho
    list_display = ('roll_no', 'naam', 'email', 'course') 

# Ab Model aur uski Admin class dono ko ek sath register karo
admin.site.register(Student, StudentAdmin)
```

✔ Admin list view me ye columns show honge  
✔ Table view customize hota hai

---

## Notes

- Tuple/list me field names dete hain
- Model ke fields hone chahiye (ya custom methods)
- Readability improve hoti hai

---

# Django Admin – search_fields Ka Use

## search_fields kya karta hai?

`search_fields` admin panel me **search box enable** karta hai. jisse records ko given fields ke basis par search kar sakte hain.

search_fields Django Admin ka ek aisa tool hai jisse aapki website ke admin panel mein ek "Search Bar" (khojne ka dabba) ban jata hai, taaki aap database ke bade data mein se kisi bhi record ko aasaani se dhoodh sakein.

---

## Important Example
```python
from django.contrib import admin
from .models import Product

@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    # Humne Django ko bataya ki in teen cheezon se search hona chahiye
    search_fields = ['name', 'brand', 'color']

# Jaise hi aap upar wala code likhenge, Django Admin panel mein upar ek Search Box aa jayega.
```

## Notes

- Text based fields best (CharField, TextField)
- Related fields bhi use kar sakte ho (relation__field)
- Partial matching support hoti hai
---

# Django Admin – list_filter Kya Karta Hai?

`list_filter` admin list page me **side filters add** karta hai. taaki records ko quickly filter kar sakein.

Iska use karne se Django Admin panel mein sidhe (right) haath ki taraf ek "Filter Box" ban jata hai, jahan aap click karke data ko category, date, ya status ke hisab se baant (filter kar) sakte hain.

## Important Example
```python
class ProductAdmin(admin.ModelAdmin):
    list_filter = ("is_active", "created_at")
```

✔ Right side filter panel show hota hai  
✔ Click karke data filter kar sakte ho

## Notes
- Boolean, Date, Choice, FK fields ke saath useful
- Quick filtering UI milti hai
- Large data sets me helpful
---

# Django – Authentication vs Authorization Difference

## Authentication kya hota hai?

Authentication = **user ki identity verify karna**  
Matlab user kaun hai — ye check karna.

Example:
- Username + password check
- Login process
- User valid hai ya nahi

---

## Authorization kya hota hai?

Authorization = **user kya kar sakta hai** — ye decide karna.

Example:
- User page access kar sakta hai?
- Edit/delete permission hai?
- Admin rights hain?

## Simple Line

Authentication → Who are you?  
Authorization → What can you do?

## Example Flow

1️⃣ User login karta hai → Authentication  
2️⃣ System check karta hai ki edit allowed hai? → Authorization

## Key Differences

| Point | Authentication | Authorization |
|--------|----------------|----------------|
Purpose | Identity verify | Permission check |
Stage | First | After auth |
Example | Login | Access control |
Checks | User valid? | Allowed action? |

## Interview Line

Authentication verifies user identity, while authorization determines what actions the user is allowed to perform.

---

# Django – Authentication System (Authentication System Overview) Kaise Kaam Karta Hai?

Django built-in authentication system deta hai jo handle karta hai:

- User model
- Login / logout
- Password hashing
- Sessions
- Permissions

---

## Core Components

- User model
- authenticate()
- login()
- logout()
- request.user
- permissions system

---

## Login Flow

### Step 1 – Credentials Check
```python
from django.contrib.auth import authenticate

user = authenticate(request, username=u, password=p)
```

✔ Username/password verify karta hai  
✔ Valid → user object return  
❌ Invalid → None

## Step 2 – Login
```python
from django.contrib.auth import login
login(request, user)
```
✔ User session create hoti hai  
✔ User logged-in ho jata hai

## Step 3 – Access User
```python
request.user
```
✔ Current logged-in user object  
✔ AnonymousUser if not logged in

## Logout
```python
from django.contrib.auth import logout
logout(request)
```
✔ Session clear  
✔ User logout

## Password Security

- Passwords hashed store hote hain
- Plain text store nahi hota
- Django hashers use karta hai

## Permissions & Groups

- User permissions assign
- Groups create
- Role based access control

## Key Points

- Built-in auth system
- Session based login
- Password hashing default
- request.user everywhere available
- Permissions + groups support


# Django – @login_required Kya ha or Karta Hai?

@login_required ek decorator hai jo ensure karta hai ki view sirf **logged-in users** hi access kar saken. Agar user login nahi hai → login page par redirect ho jayega.

@login_required restricts a view so only authenticated users can access it.

## Important Example
```python
from django.contrib.auth.decorators import login_required
from django.shortcuts import render

@login_required # Bas views ke upar ye likhna hai
def dashboard(request):
    # Ye page tabhi khulega jab user login hoga
    return render(request, 'dashboard.html')
```

## Notes
- Views ko protect karta hai
- Login URL settings se control hota hai
- Security best practice
---

# Django – Permissions & Groups Kya Hote Hain?

Permissions decide karte hain ki user **kaunsa action perform kar sakta hai**.

Permissions ye teh karti hain ki koi user database ke data ke sath kya-kya kar sakta hai. Django mein har ek model (table) ke liye automatic 4 tarah ki basic permissions banti hain (jise CRUD kehte hain):

Example:
- View: Data dekhne ki ijaazat.
- Add: Naya data daalne ki ijaazat.
- Change: Purane data ko edit/badalne ki ijaazat.
- Delete: Data ko mitaane ki ijaazat.

---

## Groups kya hote hain?

Group = permissions ka collection

Ek group me multiple permissions assign karte hain. fir users ko group assign kar dete hain.

> Maan lijiye aapki website par 50 employees hain jo content likhte hain (Writers) aur 10 employees hain jo unhe check karte hain (Editors). Agar aap har ek single user ko alag-alag jaakar permissions denge, toh bahut waqt barbad hoga. Yahan kaam aata hai Groups. Groups ka matlab hai alag-alag permissions ka ek packet ya dabba. Aap ek group banate hain, usme kuch permissions daal dete hain, aur phir users ko us group ka member bana dete hain.

## Benefits

- Role-based access
- Easy permission management
- Reusable permission sets
- Admin panel se manage

## Interview Line

Permissions control what actions a user can perform, and groups are collections of permissions assigned to multiple users.

# Security – XSS Kya Hai?

XSS = Cross-Site Scripting

Attack jisme attacker malicious JavaScript code website me inject karta hai jo dusre users ke browser me run hota hai.

website par hone wala ek aisa cyber attack hai jisme ek hacker aapki website ke andar apna fake ya khatarnak JavaScript code inject kr deta hai.

## Example

Comment box me script inject:
<script>alert("hack")</script>

## Damage
- Cookie steal
- Session hijack
- Redirects
- Data theft

## Django Protection

- Auto HTML escaping
- Template escaping default
- |safe carefully use karo

# Django REST Framework (DRF) 

## Definition

Django REST Framework (DRF) Django ka powerful toolkit hai jo **RESTful APIs** banane ke liye use hota hai.Ye Django ke upar built hai aur API development ko fast aur structured banata hai.

> Django Rest Framework (DRF) ek behad popular aur powerful tool (library) hai, jiska use Django ke andar APIs (Application Programming Interfaces) banane ke liye kiya jata hai.

> Normal Django kya karta hai? Wo database se data leta hai, use HTML page mein lapetta hai, aur browser ko bhej deta hai.Lekin maan lijiye aapko ek Mobile App (jaise Instagram ya Zomato) banani hai. Mobile apps HTML nahi samajhte, unhe chahiye hota hai Raw Data (aasan bhasha mein JSON format).DRF yahi kaam karta hai. Wo aapke Django database ke data ko ek aise format (JSON) mein badal deta hai jise duniya ka koi bhi software, app, ya frontend framework (React, Vue, Flutter) aasaani se samajh sake.

## Features

- API banane ke tools
- Serialization support
- Authentication system
- Browsable API UI
- Permissions & throttling
- Class based API views

## Use Case

- Mobile apps backend
- React / Vue frontend APIs
- Third-party integrations
---

# Serializer kya hota hai?

Serializer Django model/data ko convert karta hai:

Python object → JSON  
JSON → Python object

> Serializer ek "Translator" (Anuvaadak) ya "Converter" ki tarah kaam karta hai. Iska main kaam hota hai Django ke database wale data (Python Objects) ko ek aise format mein badalna jise mobile apps aur frontend frameworks (jaise React, Flutter, Android) aasaani se samajh sakein, aur wo format hota hai JSON.

## 1. Serialization (Database ==> JSON):- 

`Jab koi mobile app aapki website se data maangti hai (jaise user ki profile details), toh Django database se complex Python objects nikaalta hai. Serializer is complex data ko JSON format (plain text) mein badal deta hai, taaki use internet par bheja ja sake.`

## 2. Deserialization (JSON ==>Database)

`Jab koi naya user mobile app par form bhar kar "Submit" karta hai, toh app se data JSON format mein aata hai. Serializer is JSON data ko pehle check (validate) karta hai ki data sahi hai ya nahi, aur phir use wapas Python object mein badal kar database mein save kar deta hai.`

## Example

class ProductSerializer(serializers.Serializer):
    name = serializers.CharField()
    price = serializers.IntegerField()

## Kaam

- Data validation
- Data conversion
- JSON output/input handling
---

# ModelSerializer kya hota hai?

ModelSerializer = Serializer ka shortcut version, jo directly **Django model se fields auto generate** karta hai.

Agar aap normal Serializer use karte hain, toh aapko database ke har ek field ko baar-baar khud se define karna padta hai, jo bahut boring aur lamba kaam hai. ModelSerializer aapke Django Model ko dekh kar apne aap samajh jata hai ki kaun-kaun si fields hain aur unhe kaise convert karna hai.

## Example

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = "__all__"      # Isse saari fields apne aap aa jayengi!

## Benefits
- Less code
- Model based fields auto
- Built-in create/update
- Fast CRUD APIs

---

# APIView vs ViewSet Difference

`APIView aur ViewSet Django Rest Framework (DRF) mein views likhne ke do alag tareeqe hain. Inka main kaam HTTP requests (GET, POST, PUT, DELETE) ko handle karna hota hai, lekin dono ke kaam karne ka andaz bilkul alag hai.`

## APIView (Manual Control)

> APIView bilkul Django ke normal Class-Based View jaisa hota hai. Isme aap explicitly batate hain ki get() request aane par kya hoga aur post() request aane par kya hoga.

- Kaise kaam karta hai: Aapko alag-alag functions banane padte hain jaise def get(), def post(), def put(), def delete().

- Kab use karein: Jab aapko kisi API par poora control chahiye ho, ya aap koi aisi API bana rahe hain jisme seedhe database (CRUD) ka kaam nahi hai (jaise Login API, OTP verification API).

### Example
```python
from rest_framework.views import APIView
from rest_framework.response import Response

class MyAPIView(APIView):
    def get(self, request):
        return Response({"message": "Ye GET request hai"})

    def post(self, request):
        return Response({"message": "Ye POST request hai"})
```

## ViewSet

`ViewSet ko is tarah design kiya gaya hai ki aapko database ke operations (Create, Read, Update, Delete) ke liye alag-alag code na likhna pade.`

- Kaise kaam karta hai: Isme get() ya post() jaise functions nahi hote. Instead, isme actions hote hain jaise list() (saare data ke liye), create() (naya data save karne ke liye), retrieve() (kisi ek record ke liye), aur destroy() (delete karne ke liye).

- Kab use karein: Jab aap kisi Model par standard CRUD operations (vahi purana data dikhana, save karna, delete karna) kar rahe hon. Agar aap ModelViewSet use karte hain, toh ye saare functions bhi aapko likhne nahi padte, DRF khud parde ke peeche bana deta hai!

### Example
```python
from rest_framework import viewsets
from .models import Student
from .serializers import StudentSerializer

class StudentViewSet(viewsets.ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer
    # Bas itne se code mein GET, POST, PUT, DELETE sab automatic ban gaya!
```
---

## Difference Table

| Feature | APIView | ViewSet |
| :--- | :--- | :--- |
| **Control** | **Full Control** (Aap har line khud likhte hain). | **Less Control** (Bana-banaya structure milta hai). |
| **Methods** | `get()`, `post()`, `put()`, `delete()` | `list()`, `create()`, `retrieve()`, `destroy()` |
| **URLs** | Har ek URL manually `urls.py` mein add karna padta hai. | DRF ka `Router` automatic saare URLs bana deta hai. |
| **Code Size** | Zyada code likhna padta hai (Boilerplate code). | Bahut kam code mein kaam ho jata hai. |
| **Best For** | Custom logic, Login, Third-party API integration. | Standard Database CRUD operations (e.g., Product list, User Profile management). |

---

#  Authentication in APIs Kaise Hoti Hai?
API authentication me client (mobile app, frontend) apni identity verify karta hai server ko, taaki server decide kar sake ki request valid hai ya nahi.

APIs mein Authentication (Pehchan) ka matlab hota hai website ya server ko ye batana ki: "Main kaun hoon." Jab aap kisi mobile app ya frontend website se backend API par request bhejte hain, toh server har request ke sath aapki identity check karta hai. Agar identity sahi hai, toh data milta hai; nahi toh 401 Unauthorized ka error aa jata hai.

APIs stateless hoti hain (yaani server yaad nahi rakhta ki aapne pehle login kiya tha ya nahi), isliye har ek request ke sath authentication ka saboot bhejna padta hai.

### ✅ Token Authentication
Ye sabse zyada use hone wala tarika hai. Isme jab user pehli baar username aur password daal kar login karta hai, toh server use ek lamba sa secret code deta hai jise Token kehte hain.

- Kaise kaam karta hai: Mobile app us token ko apne paas safe (save) kar leti hai. Agli baar jab bhi app koi data maangti hai (jaise user ki profile), toh wo us token ko request ke Header mein daal kar bhejti hai.

- Fayda: Har baar password bhejne ki zaroorat nahi padti.

---

### ✅ JWT Authentication
Ye Token Authentication ka hi ek advanced aur modern roop hai. Normal token mein server ko baar-baar database mein check karna padta hai ki token sahi hai ya nahi, lekin JWT smart hota hai.

- Kaise kaam karta hai: JWT token ke andar hi user ki saari information (jaise User ID, Name) ek encrypted format mein pehle se chhupi hoti hai. Server bina database ko touch kiye, sirf us token ko decode karke samajh jata hai ki user kaun hai.

- Fayda: Ye bahut fast hota hai aur bade projects (Microservices) mein sabse best maana jata hai.

---

### ✅ Session Authentication
Agar aap normal Django website chala rahe hain (jahan frontend aur backend ek hi project mein hain), toh Django iska use karta hai.

- Kaise kaam karta hai: Jab aap login karte hain, toh server browser mein ek Session ID (Cookie) save kar deta hai. Browser har request ke sath wo cookie apne aap server ko bhejta rehta hai.

- Kamzori: Ye mobile apps (Android/iOS) ke sath acche se kaam nahi karta, isliye pure APIs ke liye ise avoid kiya jata hai.

---

### ✅ Basic Authentication
Ye sabse purana aur aasan tarika hai, lekin ise real projects mein use nahi kiya jata (sirf testing ke liye sahi hai).

- Kaise kaam karta hai: Isme har ek API request ke sath aapka Username aur Password base64 encoded format mein header ke andar bheja jata hai.

- Kamzori: Kyunki har request mein password ja raha hai, agar kisi ne request beech mein pakad li (intercept kar li), toh aapka password leak ho jayega.

---

## DRF Settings Example
```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        # JWT Token use karne ke liye
        'rest_framework_simplejwt.authentication.JWTAuthentication',
        
        # DRF ka normal token use karne ke liye
        'rest_framework.authentication.TokenAuthentication',
    ]
}
```
---

#  Status Codes Examples

## Success Codes

200 → OK  
201 → Created  
204 → No Content

---

## Client Error Codes

400 → Bad Request  
401 → Unauthorized  
403 → Forbidden  
404 → Not Found

---

## Server Error Codes

500 → Internal Server Error  
503 → Service Unavailable

---

# Django – Caching, Middleware, Static/Media, Deployment Basics

---

# Caching kya hoti hai Django me?

Caching ka matlab hai frequently used data ko temporarily store karna taaki har baar database ya heavy processing na karni pade.

Caching (कैशिंग) Django mein website ki speed aur performance ko badhane ka ek behtareen tareeqa hai.

Caching ek aiysa technique (tarika) hai jisme hum kisi heavy ya baar-baar maange jaane wale data ko ek temporary fast-memory (jaise RAM) mein save kar lete hain. Taaki jab agli baar wahi data chahiye ho, toh hume poori calculation ya database query dobara na karni pade, aur data turant mil jaye.

> Database se data nikaalne mein time lagta hai, Cache se nikaalne mein nahi. Isiliye baar-baar chahiye hone wale data ko temporary memory mein rakhna hi Caching hai.

### Iska Fayda Kya Hota Hai? (Kyun Use Karte Hain?):-

- Website Speed Badhti Hai (Low Latency): User ko page khulne ke liye intezar nahi karna padta, data micro-seconds mein load ho jata hai.

- Server Par Load Kam Hota Hai: Database ko baar-baar bade aur heavy chakkar (queries) nahi kaatne padte, jisse server crash nahi hota.

- Cost Bachti Hai: Server ke resources kam use hote hain, toh kharcha bhi kam aata hai.

## Django Me Cache Types

- Per-site: Portfolio/Blog website ke liye jahan sab same hai.
- Per-view: Kisi ek heavy page (jaise Report ya Chart page) ke liye.
- Template Fragment: Header/Footer ya Navigation bar ke liye.
- Low-level: Kisi third-party API data ya complex Python calculation ko save karne ke liye.

---

## Per-site cache (Pura Site Cache)

Jab aapki poori website hi aisi ho jisme data roz-roz nahi badalta, toh aap poori website ko hi cache kar dete hain.

- Real-life Example (Company Website): Maan lijiye ek IT Company ki website hai (About Us, Services, Contact Us pages). Is website ka data mahino tak badalta nahi hai.

- Kaise kaam karega: Aap settings mein per-site cache active kar denge. Ab koi bhi user kisi bhi page par jaye, Django bina database touch kiye poori website RAM se turant chala dega.

## Per-view cache (Specific View Cache)

Jab poori website toh dynamic hai (badalti rehti hai), lekin kuch khas pages aise hain jo bahut heavy hain aur unka data har 5 ya 10 minute mein hi badalta hai.

- Real-life Example (News or Stock Dashboard): Ek News Website hai jiska Home Page har second badalta hai, lekin uska "Gold Price Today" wala page din mein bas ek-do baar badalta hai.

- Kaise kaam karega: Aap sirf us Gold Price wale view par @cache_page decorator laga denge.

```python
from django.views.decorators.cache import cache_page

@cache_page(60 * 15) # 15 minute ke liye cache
def gold_price_view(request):
    # Heavy database query ya external API call
    return render(request, 'gold.html')
```
## Template fragment cache (Template ka part cache)
Jab aap poore page ko cache nahi kar sakte kyunki page par user ka naam ya cart items dikh rahe hain (jo har user ke liye alag hain), lekin page ka ek bada hissa sabke liye bilkul same hai.

- Real-life Example (E-commerce Home Page): Page ke upar dikhta hai "Welcome, Rahul!" (Ye cache nahi ho sakta). Lekin neeche ek bada "Footer" hai jisme company ki links hain ya ek "Trending Products Sidebar" hai jo sabko same dikhta hai aur database se heavy query karke aa raha hai.

- Kaise kaam karega: Aap HTML file ke andar sirf us heavy hisse ko cache block mein daal denge.
```html
{% load cache %}

<h1>Welcome, {{ user.username }}</h1> 

{% cache 7200 trending_sidebar %}
    <div class="sidebar">
        <h3>Trending Products</h3>
        </div>
{% endcache %}
```
## Low-level cache (Manual Key-Value Cache)
Jab aapko na toh page cache karna hai, na HTML. Aapko sirf kisi ek bade data (Variable/List/QuerySet) ko cache karna hai jise nikaalne mein bahut time lagta hai, aur aap use code mein manually control karna chahte hain.

- Real-life Example (Weather API Data): Aapki website par ek chota sa mausam (Weather) ka widget hai, jo kisi doosri website (Third-party API) se har shehar ka temperature lata hai. Har request par doosri website ko hit karna slow aur mehenga padega.

- Kaise kaam karega: Aap code mein khud check karenge ki cache mein data hai ya nahi.

```python
from django.core.cache import cache

def get_weather_data(request):
    # 1. Pehle cache mein 'delhi_weather' naam ki KEY dhoodho
    weather = cache.get('delhi_weather')
    
    if not weather:
        # 2. Agar cache mein nahi mila, toh bahar ki API se nikaalo (Heavy step)
        weather = call_expensive_weather_api(city="Delhi")
        
        # 3. Agle 1 ghante (3600 seconds) ke liye cache mein save kar do
        cache.set('delhi_weather', weather, 3600)
        
    return render(request, 'weather.html', {'weather': weather})
```
---

# Middleware kya hota hai?

Middleware ek layer hoti hai jo **request aur response ke beech process** karti hai.

Middleware Django mein ek 'Request/Response Processor' hota hai. Ye ek aisa plugin system hai jo Django ke request aane (Request Phase) aur response jaane (Response Phase) ke beech mein baithta hai. Iska main kaam poori website par globally security, logging, authentication, ya data manipulation ko apply karna hota hai.

## The Architecture/Lifecycle

- Inbound (Request): Jab browser se request aati hai, toh wo View tak pahonchne se pehle middleware se guzarti hai. Yahan hum request ko modify kar sakte hain ya user ko block kar sakte hain.

- Outbound (Response): Jab View apna kaam khatam karke HTML ya JSON response banta hai, toh wo user tak pahonchne se pehle wapas middleware se guzarta hai. Yahan hum response headers badal sakte hain ya data compress kar sakte hain.

## Use Cases

- Authentication & Authorization: Har request par check karna ki user logged in hai ya nahi (jaise Django ka in-built AuthenticationMiddleware).

- IP Blocking / Rate Limiting: Agar koi hacker lagatar attack kar raha hai, toh uski IP ko View tak pahonchne se pehle hi block kar dena.

- Maintenance Mode: Poori website ko ek click mein rok kar sabhi users ko "Under Maintenance" ka page dikhana.

-Execution Time Logging: Ye napna ki kaun sa URL ya View load hone mein kitna time le raha hai (Performance monitoring).

#### Counter Question ke liye taiyar rahein: "Settings.py mein Middleware ka ORDER kyun zaroori hai?"

- Aapka Jawab: Middleware hamesha TOP-to-BOTTOM (upar se neeche) execute hote hain request ke waqt.

- Agar aapne AuthenticationMiddleware se upar koi aisa middleware rakh diya jise user ki detail chahiye, toh wo error de dega kyunki user ki pehchan toh neeche wale middleware ne ki hi nahi. Isliye order bahut critical hota hai.

---

# Static vs Media Files Difference

### Static Files

`Static files wo files hoti hain jo Developer website banate waqt khud project mein daalta hai. Ye website ke look, feel aur functionality ke liye zaroori hoti hain. Jab website live (production) hoti hai, toh ye files badalti nahi hain (static rehti hain).`

Static = fixed files jo developer provide karta hai

Examples:
- CSS
- JS
- Images (design)
- Fonts

### Media Files
`Media files wo files hoti hain jo website live hone ke baad Users upload karte hain. Ye files dynamic hoti hain kyunki har user alag file upload karega, aur ye lagatar database/storage mein badhti rehti hain.`

Media = user uploaded files

Examples:
- Profile images
- Documents
- Uploads
---

## Settings Example

STATIC_URL = "/static/"
MEDIA_URL = "/media/"

| Feature | Static Files | Media Files |
| :--- | :--- | :--- |
| **Kiska data hai?** | **Developer** ka (Website ke design ke liye). | **User** ka (Website par upload kiya gaya content). |
| **Examples** | CSS, JavaScript, Website Logo, Fonts. | Profile Pictures, Product Images, Uploaded PDFs. |
| **Kab milti hain?** | Website deploy hone se pehle hi project mein hoti hain. | Website live hone ke baad users ke through aati hain. |
| **Django Command** | Inhe ik इकट्ठा karne ke liye `python manage.py collectstatic` chalana padta hai. | Iske liye koi command nahi chahiye, ye real-time mein save hoti hain. |
| **Settings Rules** | `STATIC_URL` aur `STATIC_ROOT` ka use hota hai. | `MEDIA_URL` aur `MEDIA_ROOT` ka use hota hai. |

---

#  collectstatic command kya karti hai?

Ye command saari static files ko **different apps se collect karke ek single folder me copy** karti hai. Production deployment me use hoti hai.

collectstatic ka kaam website ke saare kops (corners) mein bikhri hui Static Files (CSS, JS, Images) ko dhoodh kar ek single, alag folder mein ik इकट्ठा (collect) karna hai.

## Command
```python
python manage.py collectstatic
```

### Humein iski zaroorat kyun padti hai?
Jab aap Django par website bana rahe hote hain (Development ke waqt), toh aapki static files alag-alag jagah bikhri hoti hain:

- Kuch files aapke main project ke static/ folder mein hoti hain.
- Kuch files aapke alag-alag apps (jaise blog, accounts) ke andar hoti hain.
- Kuch files Django ke apne andar hoti hain (jaise Django Admin Panel ki apni CSS aur JS files).

Development ke waqt Django itna smart hota hai ki wo in sabhi alag-alag jagah se files ko dhoodh kar browser par dikha deta hai.
Lekin jab aap website ko Live (Production) karte hain (jaise AWS, Heroku ya VPS par), toh Django static files handle nahi karta kyunki wo slow ho jayega. Live website par static files ko handle karne ka kaam Nginx, Apache ya AWS S3 jaise fast servers ko diya jata hai. Ab Nginx ko kya pata ki aapki files kahan-kahan bikhri hain? Use toh saari files ek hi jagah chahiye.

### summery

`Development ke waqt static files alag-alag apps mein bikhri hoti hain. Jab hum website ko live (production) karte hain, toh hum python manage.py collectstatic command chalate hain, jo saari static files ko ek single centralized folder (STATIC_ROOT) mein jama kar deti hai, taaki Nginx ya AWS S3 jaisa external web server unhe aasaani se serve kar sake.`

# Environment Variables kyu use karte hain?

Environment variables external config values hoti hain jo code ke bahar store hoti hain.

Environment Variables ka use hum Sensitive Data aur Configuration Settings ko apne core source code se alag (isolate) rakhne ke liye karte hain. Ye variables direct code mein hardcode nahi hote, balki us operating system ya server ke environment mein save hote hain jahan hamara project chal raha hota hai.

---

## Kyu Use Karte Hain?

### Security:-
Aapke project mein bahut saari aiysi cheezein hoti hain jo agar leak ho gayin toh aapka poora system hack ho sakta hai. Jaise:

- Django ki SECRET_KEY
- Database ka Password
- Payment Gateway (Razorpay/Stripe) ki API Keys

### Environment Management (Local vs Production):-
Aapki website jab aapke computer par chalti hai (Local/Development) aur jab live server par chalti hai (Production), toh dono ki settings alag hoti hain:

- Local Computer par: DEBUG = True hota hai, aur database SQLite use hota hai.
- Live Server par: DEBUG = False hona chahiye, aur database PostgreSQL ya MySQL use hota hai.

Agar aap baar-baar code mein jaakar ye badlenge, toh galti hone ka chance rahega. Env variables se aap computer ko bol sakte hain: "Agar local computer hai toh local settings uthao, agar live server hai toh live settings."

### Code Reusability (Code ko aasan banana):-

Aapka code bilkul saaf-suthra rehta hai. Kal ko agar aapko database ka password badalna hai, toh aapko apna poora Django ka code edit karne aur use dubara deploy karne ki zaroorat nahi hai. Aap bas server par jaakar environment variable ki value badal denge, aur kaam ho jayega.

---

## Examples

- SECRET_KEY
- DB password
- API keys
- Email credentials

---

## Django Mein Ise Kaise Use Karte Hain?
```python
# .env file
DJANGO_SECRET_KEY=my-super-secret-key-123
DB_PASSWORD=my_secure_password
DEBUG_STATUS=False
```

```python
# .env file
DJANGO_SECRET_KEY=my-super-secret-key-123
DB_PASSWORD=my_secure_password
DEBUG_STATUS=False
```

## Interview Line

Environment Variables ka use hum sensitive data (jaise passwords, API keys) ko code se alag rakhne ke liye karte hain. Isse hamara code secure rehi hai, hum use GitHub par bina dare share kar sakte hain, aur local ya production server ke beech mein settings ko aasaani se switch kar sakte hain.


# Django – Advanced Practical Interview Notes

---

# Form submit hone ke baad duplicate entry kaise rokoge?

### 1. PRG Pattern (Post/Redirect/Get) — Sabse Zaroori Backend Tarika
Yeh server-side ka sabse standard aur pehla solution hai. Jab bhi koi form POST request ke zariye submit ho, toh data save karne ke baad kabhi bhi direct HTML template render (render()) mat karo. Uske badle user ko kisi doosre page par Redirect (redirect()) kar do.

- Bina PRG ke galti ❌: User ne form submit kiya, data save hua, aur aapne render(request, 'success.html') kar diya. Ab agar user ne page refresh kiya, toh browser dubara wahi POST request (data) bhej dega aur duplicate entry ho jayegi.

- PRG ke sath sahi tarika  : User ne form submit kiya, data save hua, aur aapne return redirect('success_page') kar diya. Redirect hone se browser ek nayi GET request chala deta hai. Ab agar user refresh karega bhi, toh sirf success page dobara load hoga, data dobara submit nahi hoga.

```python
from django.shortcuts import render, redirect

def submit_form(request):
    if request.method == "POST":
        form = MyForm(request.POST)
        if form.is_valid():
            form.save()
            # SUCCESS: Hamesha redirect karo, render nahi!
            return redirect('success_url') 
    else:
        form = MyForm()
    return render(request, 'form.html', {'form': form})
```
### 2. JavaScript se Submit Button ko Disable karna — Frontend Tarika:-
User ki us galti ko roko jahan wo double-click ya triple-click kar deta hai. Jaise hi user pehli baar "Submit" button par click kare, JavaScript ka use karke us button ko turant Disable kar do taaki wo dubara click hi na kar paye.

### 3. Database Level Constraints (Unique fields) — Ultimate Security
Maan lijiye user ne kisi tarah JavaScript bhi bypass kar di aur refresh bhi kar diya, toh aakhri security aapka Database hota hai. Aap model mein fields ko unique=True ya UniqueConstraint de sakte hain.

## Summery:-

- Backend par PRG Pattern (Post/Redirect/Get): Form data save karne ke baad hum direct render karne ke bajaye user ko redirect kar dete hain, jisse page refresh karne par data dubara submit nahi hota.

- Frontend par JavaScript: Form submit hote hi hum JavaScript se submit button ko disabled kar dete hain taaki user accidental double-clicks na kare.

- Database Level par: Hum critical fields par unique=True ya UniqueConstraint lagate hain taaki back-to-back requests aane par bhi database duplicate data accept na kare."
---

# Large QuerySet ko optimize kaise karoge?

## Techniques

### ✅ select_related()
ForeignKey / OneToOne joins optimize

### ✅ prefetch_related()
ManyToMany / reverse relations optimize

### ✅ only() / defer() — Sirf zaroori Columns mangaao
- only(): Django ko bolta hai ki database se sirf ye specific columns hi lekar aao.

- defer(): Django ko bolta hai ki baaki sab le aao, bas in columns ko chhod do (jaise koi heavy TextField ya description).

### ✅ pagination
Kabhi bhi lakhon data ek page par mat dikhao. Hamesha Paginator use karo taaki ek baar mein sirf 20 ya 50 records hi load hon.

### ✅ values() aur values_list()
Django jab query karta hai, toh wo har row ke liye ek complex Python Object (Model Instance) banata hai. Isme bahut memory lagti hai. Agar aapko sirf data chahiye aur uspar Django ke functions (jaise .save()) nahi chalane, toh direct Dictionary ya Tuple manga lijiye.

- values(): Data ko Dictionary format mein lata hai.
- values_list(): Data ko Tuple format mein lata hai

### ✅ iterator()
Ye database se data ko chote-chote chunks (groups) mein uthata hai. Jaise hi ek chunk ka kaam khatam hota hai, ye use RAM se delete karke agla chunk utha leta hai.

---

# Custom User Model kab banana chahiye?

Custom User Model humein hamesha project ke bilkul shuruat mein (Day 1 par) hi bana lena chahiye. Iski zaroorat tab padti hai jab humein login ke liye Username ki jagah Email ka use karna ho, ya user table mein extra fields jaise Phone Number, Age, ya Profile Picture jodni hon. Shuru mein Custom User Model banane se hamara database future-proof ho jata hai aur baad mein migrations ka koi risk nahi rehta."

## 1. Username ki jagah Email se Login karwana ho (Sabse Common Use Case)
Django ka jo default user model hota hai, wo login karne ke liye username maangta hai. Lekin aaj kal ki modern websites (jaise Facebook, Amazon, Netflix) par log Email aur Password se login karte hain. Agar aapko apni website par email-based login chahiye, toh aapko Custom User Model banana hi padega.

## 2. Extra Fields jodni hon (Jaise Phone Number, Profile Picture)
Default Django User Model mein sirf kuch gine-chune fields hote hain—jaise username, first_name, last_name, email, aur password.
Agar aap ek e-commerce ya fintech app bana rahe hain jahan aapko user ka Phone Number, Date of Birth, Address, ya User Type (Buyer/Seller) save karna hai, toh aapko custom user model ki zaroorat padegi.

## 3. Future Flexibility (Sabse Bada Kaaran 🚀)
Maan lijiye aaj aapka project chota hai aur default user model se kaam chal raha hai. Lekin 6 mahine baad business requirements badal jaati hain aur client kehta hai ki "Humein users ke liye phone number field aur OTP login chahiye".

## Django Mein Custom User Model Banane Ke 2 Tarike:- 

### Using AbstractUser (90% waqt yahi use hota hai):-
```python
from django.contrib.auth.models import AbstractUser
from django.db import models

class CustomUser(AbstractUser):
    phone_number = models.CharField(max_length=15, blank=True)
    profile_pic = models.ImageField(upload_output='profiles/', blank=True)
```
## Using AbstractBaseUser (Full Customization)
Agar aapko Django ka default structure bilkul pasand nahi hai aur aap ekdum scratch (shuru) se apna user authentication system banana chahte hain (jaise username field ko poori tarah hatakar sirf email aur phone par authentication chalana), toh AbstractBaseUser ka use hota hai. Isme aapko apna custom UserManager bhi likhna padta hai.

> Pro-Tip: Project Shuru Karte Hi Settings Mein Ye Line Zaroor Daalein

Model banane ke baad Django ko batana padta hai ki default user model ko chhod kar hamare naye model ko use kare. Iske liye settings.py mein ye line likhi jaati hai:
```python
AUTH_USER_MODEL = 'accounts.CustomUser'  # 'app_name.ModelName'
```
---


# Subquery kya hoti hai?
Subquery ek aisi query hoti hai jo kisi doosri main SQL query (Outer Query) ke andar nested hoti hai. Django ORM mein hum Subquery expression ka use tab karte hain jab humein database level par ek query ke result ko doosri query mein as a filter ya field (annotation) use karna ho, bina saara data pehle Python memory (RAM) mein laaye.

Subquery = ek query jo dusri query ke andar run hoti hai.


### Example
```python
from django.db.models import Subquery, OuterRef
from .models import Customer, Order

# Step 1: Pehle Inner Query (Subquery) banayein
# Isme hum Customer ke sabse naye order ka amount nikaal rahe hain
latest_order_subquery = Order.objects.filter(
    customer=OuterRef('pk') # Bahaar wale customer ki ID se match karo
).order_by('-ordered_at').values('amount')

# Step 2: Main Query mein Subquery ko annotate kar dein
# [:1] likhna zaroor batayein, kyunki subquery se sirf 1 hi row aani chahiye
customers = Customer.objects.annotate(
    latest_order_value=Subquery(latest_order_subquery[:1])
)
```

# OuterRef Kya hota hai? (Sabse Important Cross-Question):- 

- Aapka Jawab: "OuterRef bilkul SQL ke Correlated Subquery ki tarah kaam karta hai. Iska kaam bahaar wali query (Outer Query) ke kisi column ya ID ko andar wali query (Subquery) ke andar access karwana hai."

- Example: Agar humein har Category ke andar uske apne Products dhoodhne hain, toh Subquery ko kaise pata chalega ki abhi kis category ki baat ho rahi hai? Wahan hum OuterRef('pk') likhte hain, jiska matlab hota hai: "Bahaar chal rahi Category ki Primary Key (ID)".

---

# Database Indexing kya hai?

Database Indexing ek performance optimization technique hai jiska use tables par SELECT queries aur search operations ki speed ko badhane ke liye kiya jata hai. Parde ke peeche database iske liye B-Tree (Balanced Tree) ya Hash data structure ka use karta hai, jisse database ko Full Table Scan nahi karna padta aur data micro-seconds mein mil jata hai."

## Indexing ke Trade-offs (Nuksan) kya hain?

- Write Operations Slow Hona: Jab bhi hum table mein koi naya data INSERT, UPDATE, ya DELETE karte hain, toh database ko asli table ke sath-sath Index Table ko bhi re-arrange/update karna padta hai. Is wajah se write operations slow ho jaate hain.

- Storage Cost: Indexing free nahi hoti; ye disk par extra space leti hai. Agar table bohot badi hai aur bohot saare indexes hain, toh index ka size asli table ke size se bhi bada ho sakta hai.

---

# Constraints kya hote hain?

Database Constraints wo rules aur restrictions hote hain jo hum database tables ke columns par apply karte hain. Inka main maqsad data ki Integrity (sachai), Accuracy, aur Consistency ko maintain karna hota hai. Yeh hamari Last Line of Defense hote hain—yaani agar frontend ya backend ka validation logic galti se bypass ho bhi jaye, toh bhi database constraints galat data ko save hone se rok dete hain.

## Types:-

- PRIMARY KEY: Yeh NOT NULL aur UNIQUE ka combination hota hai. Yeh table ke har record ko ek anokhi (unique) pehchan deta hai.
- UNIQUE (unique=True): Yeh pakka karta hai ki us column mein koi bhi duplicate value na aaye (Jaise: email ya phone_number).
- FOREIGN KEY: Yeh do tables ke beech ka relational link hota hai aur Referential Integrity pakka karta hai. Iska matlab hai ki aap child table mein koi aisi ID nahi daal sakte jo parent table mein exist hi na karti ho.
- CHECK CONSTRAINT: Yeh tab use hota hai jab hume koi custom condition lagani ho (Jaise: Age >= 18 ya Price > 0).

## Form Validation vs Database Constraints mein diff??

- Form/Serializer Validation: Yeh application level par hota hai. Yeh user ko ek acha error message ("Please enter a valid email") dikhane ke liye hota hai. Lekin agar koi developer direct Django Shell se, custom script se, ya direct database mein data insert karega, toh yeh validation kaam nahi karega.

- Database Constraints: Yeh direct database (PostgreSQL/MySQL) ke andar fit ho jaate hain. Chahe data Django se aaye, shell se aaye, ya kisi raw SQL query se—agar rule tootega, toh database error throw kar dega (IntegrityError). Yeh absolute security deta hai.


# Django thread safe hai?

Python (aur Django) mein ek Thread ek lightweight process hota hai jo ek sath multiple tasks ko execute karne ki suvidha deta hai. Default roop se, jab hum Django ka development server (runserver) chalate hain, toh wo Multi-threaded hota hai. Iska matlab ye hai ki agar ek sath 5 alag-alag users website par request bhejte hain, toh Django har ek user ki request ko handle karne ke liye ek Naya Thread bana deta hai, taaki kisi bhi user ko wait na karna pade

# GIL (Global Interpreter Lock)

Python ka GIL (Global Interpreter Lock) ek waqt par sirf ek hi thread ke CPU code ko execute hone deta hai. Is wajah se Python mein True CPU Multi-threading nahi hoti. Lekin Django websites par jo kaam hote hain, wo I/O Bound (Input/Output) hote hain—jaise Database se data nikaalna, external API ko hit karna, ya file read/write karna."

"Jab ek thread database query ka intezar kar raha hota hai, toh GIL free ho jata hai aur Python automatic doosre thread ko chalane lagta hai. Isiliye, GIL ke hote hue bhi Django mein multi-threading se performance bohot fast ho jaati hai.

---

# ✅ Migrations rollback kaise karte ho?
Django mein migrations ko rollback karne ke liye hum koi alag command nahi chalate, balki hum migrate command ka hi use karte hain, lekin uske sath hum us App ka Naam aur us Migration ka Number pass karte hain jahan tak humein wapas (backboard) jaana hai.

Migration rollback = database ko previous migration state me le jana.

## Command – Specific Migration

python manage.py migrate app_name 0003

✔ App ko migration 0003 tak wapas le jayega

---

## Rollback All Migrations

python manage.py migrate app_name zero

✔ Sab migrations undo

---

## Check Migration List

python manage.py showmigrations

### Sirf database rollback karne ke baad kya aapka kaam khatam ho jata hai?
Nahi Sir, sirf terminal par command chalane se database rollback hota hai. Uske baad humein do zaroori kaam aur karne hote hain:"

- Code/File Rollback: Humein us migration file (0005_wrong_migration.py) ko physically apne project se delete karna hoga, ya phir git revert ya git checkout karke purane code par aana hoga. Agar hum file delete nahi karenge, toh agli baar migrate chalate hi wo dubara execute ho jayegi.

- Model Sync: Humein apne models.py mein jaakar us field ya change ko bhi hatana hoga jo humne kiya tha, taaki hamara Django code aur database dono aapas mein hamesha synced rahein.
---

# Fake Migration Kya Hoti Hai?:-
Fake Migration ka matlab hota hai Django ko bevkoof banana (trick karna). Jab hum --fake flag ke sath migrate command chalate hain, toh Django database mein koi naya SQL table ya column create nahi karta. Wo sirf Django ki internal tracking table (django_migrations) mein jaakar ek entry daal deta hai ki falana migration successfully chal chuki hai.
```python
python manage.py migrate --fake <app_name> <migration_number>
```

## Iski Zaroorat Kab Padti Hai?:-
Maan lijiye aap ek puraane project par kaam kar rahe hain jahan database (jaise PostgreSQL) mein Customer naam ki table pehle se bani hui hai aur usme data bhi hai. Ab aapne us project mein Django setup kiya aur pehli baar python manage.py makemigrations chalaya. Django ne ek 0001_initial.py file bana di jisme likha hai Create Table Customer.

- Problem (Agar normal migrate chalaya ❌): Django database mein Customer table banane jayega, lekin database bolega: "Bhai, ye table toh pehle se hai!" Aur Django error dekar crash ho jayega (Table already exists).

- Solution (Fake Migration  ): Hum yahan chalayenge.
```python
python manage.py migrate --fake myapp 0001
```

## --fake-initial Kya Hota Hai?:-

> Interviewer aapko confuse karne ke liye pooch sakta hai: "Kya --fake aur --fake-initial dono same hain?"

`Nahi Sir, thoda sa farq hai. --fake zabardasti kisi bhi migration ko fake mark kar deta hai, chahe database mein table ho ya na ho.

Lekin --fake-initial thoda smart hai. Yeh sirf 0001_initial migration par kaam karta hai. Ye database ko khud check karta hai—agar table pehle se bani hui hai, toh ye use automatic --fake (skip) kar deta hai, aur agar table nahi bani, toh ye use normal migrate (create) kar deta hai.`

# ✅ debug=True production me kyu nahi?

### 1. Interactive Detailed Error Page (Hacker's Paradise 🏴‍☠️)
Development ke waqt jab code mein koi error aata hai, toh Django browser par ek yellow rang ka detailed error page dikhata hai. Yeh developer ke liye toh vardan hai, lekin production par hacker ke liye ek khazana hai.

- Bina Debug ke (Production Ready ✅): User ko ek simple 500 Internal Server Error ka saaf-suthra page dikhega.

- Debug = True ke sath (Khatra ❌): Agar production par koi error aaya, toh poori duniya ko aapka internal code dikh jayega.

### 2. Information Leak on 404 Pages
Agar DEBUG = True hai aur koi user kisi aise URL par jata hai jo exist nahi karta, toh Django use sirf "Page Not Found" nahi bolta. Wo screen par un saare URL patterns ki list (urlpatterns) print kar deta hai jo aapne urls.py mein likhe hain.

Isse hacker ko aapke hidden admin panels (/secret-admin-login/) ya internal API endpoints ka pata chal jata hai.

### 3. Performance & Memory Leak (RAM issues)
Logon ko lagta hai ki debug mode sirf security ke liye band kiya jata hai, lekin iska performance par bhi bohot bura asar padta hai:

- SQL Logging: Jab DEBUG = True hota hai, toh Django parde ke peeche chalne wali har ek SQL query ko apni memory (RAM) mein save karke rakhta hai (connection.queries).

- Production par asar: Production par jab ek sath hazaron users aayenge aur lakhon queries chalengi, toh Django un sabka log RAM mein rakhne ki koshish karega. Isse aapke server ki RAM bohot jaldi full ho jayegi aur server Out of Memory (OOM) hokar crash ho jayega.

