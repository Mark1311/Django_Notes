# Django – Template Inheritance Kya Hota Hai?

## Template Inheritance kya hai?

Template inheritance Django ka feature hai jisme:
Ek **base template** banate hain (common layout)  
Aur baaki templates usko **extend** karte hain.

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

{% block content %}{% endblock %}

---

### child.html

{% extends "base.html" %}

{% block content %}
<h1>Home Page</h1>
{% endblock %}

✔ child template base ko extend karta hai  
✔ block content ko replace karta hai

---

## Important Tags

### extends
Parent template ko inherit karta hai

{% extends "base.html" %}

### block
Replaceable section define karta hai

{% block content %} {% endblock %}

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

## Interview Line

Template inheritance allows child templates to reuse and extend a base template layout using extends and block tags.


# Django Templates – block & extends Ka Use

## extends ka Use

`extends` tag ka use child template ko **base template inherit** karwane ke liye hota hai.

Matlab:
Child template → parent (base.html) ka layout reuse karega.

### Example

{% extends "base.html" %}

✔ Hamesha template ki first line me likhte hain  
✔ Base layout load karta hai

---

## block ka Use

`block` tag base template me **replaceable section** define karta hai.  
Child template is block ko override (replace) karta hai.

### Base Template

{% block content %}{% endblock %}

---

### Child Template

{% block content %}
<h1>Dashboard Page</h1>
{% endblock %}

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

## Interview Line

extends is used to inherit a base template, and block is used to define and override replaceable sections in child templates.


# Django – Template Filters Kya Hote Hain?

## Template Filters kya hote hain?

Template filters Django templates me use hote hain data ko **format / modify** karne ke liye — directly template ke andar.

Matlab:
Variable ka display change karna bina Python code likhe.

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

## Custom Filters

- Khud ke filters bhi bana sakte ho
- Python file + template tag registration se

---

## Key Points

- Template level data formatting
- Pipe operator use hota hai
- Clean templates without logic clutter
- Built-in + custom filters possible

---

## Interview Line

Template filters are used to transform or format variables inside Django templates using the pipe (|) syntax.


# Django – Custom Template Filter Kaise Banate Hain?

## Custom Template Filter kya hota hai?

Jab built-in template filters enough na ho, tab hum apna khud ka filter banate hain  
taaki template me custom data formatting kar sakein.

---

## Steps to Create Custom Template Filter

### Step 1 – app ke andar folder banao

your_app/
    templatetags/
        __init__.py
        custom_filters.py

✔ templatetags folder ka naam exact yehi hona chahiye  
✔ __init__.py required hai

---

## Step 2 – Filter Function Likho

custom_filters.py

from django import template

register = template.Library()

@register.filter
def double(value):
    return value * 2

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

@register.filter
def add_num(value, num):
    return value + num

Template:

{{ price|add_num:10 }}

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

## Interview Line

Custom template filters are created using a templatetags module and registered with @register.filter to extend Django template functionality.


# Django – Static Files Kaise Load Karte Hain?

## Static Files kya hoti hain?

Static files = wo files jo fixed hoti hain:

- CSS
- JS
- Images
- Fonts

Ye templates me use hoti hain design aur UI ke liye.

---

## Step 1 – settings.py Me Config

INSTALLED_APPS me ensure karo:

'django.contrib.staticfiles'

---

## Step 2 – Static Folder Structure

your_app/
    static/
        your_app/
            style.css
            logo.png

✔ Best practice: static/app_name/…

---

## Step 3 – Template Me Static Load Karo

Template ki top par:

{% load static %}

---

## Step 4 – Static File Use Karo

CSS:

<link rel="stylesheet" href="{% static 'your_app/style.css' %}">

Image:

<img src="{% static 'your_app/logo.png' %}">

JS:

<script src="{% static 'your_app/app.js' %}"></script>

---

## Important Settings (Basic)

STATIC_URL = "/static/"

---

## Production Me

collectstatic command use hota hai:

python manage.py collectstatic

✔ Saari static files ek jagah collect ho jati hain

---

## Key Points

- {% load static %} required hai template me
- static tag path resolve karta hai
- Static = CSS, JS, images
- App-wise folder structure recommended
- collectstatic production me use hota hai

---

## Interview Line

Static files are loaded in Django templates using {% load static %} and the {% static %} template tag to reference CSS, JS, and image files.


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

### Example

from django import forms

class ContactForm(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()

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

ModelForm = Django Form + Database Model auto link

Model ke fields se form automatically ban jata hai.

### Example

from django.forms import ModelForm
from .models import Product

class ProductForm(ModelForm):
    class Meta:
        model = Product
        fields = "__all__"

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

| Point | HTML Form | Django Form | ModelForm |
|--------|------------|--------------|------------|
Defined In | HTML | Python | Python |
Validation | Manual | Built-in | Built-in |
DB Link | ❌ | ❌ | ✅ |
Code Size | Medium | Medium | Least |
Security | Basic | Strong | Strong |
Auto Fields | ❌ | ❌ | ✅ |

---

## Quick Interview Line

HTML forms are manual, Django Forms add validation and structure, and ModelForms automatically create forms from database models.


# Django – Form Validation Kaise Hoti Hai?

## Form Validation kya hoti hai?

Form validation ka matlab hai:
User ne jo data submit kiya hai wo:

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

form = ContactForm(request.POST)

if form.is_valid():
    data = form.cleaned_data

✔ is_valid() → validation trigger karta hai  
✔ cleaned_data → validated data deta hai

---

## Built-in Field Validation

from django import forms

class MyForm(forms.Form):
    email = forms.EmailField()
    age = forms.IntegerField(min_value=18)

✔ Type check  
✔ Required check  
✔ Min/Max rules

---

## Custom Field Validation

Specific field validate karne ke liye:

def clean_name(self):
    name = self.cleaned_data["name"]
    if name == "admin":
        raise forms.ValidationError("Not allowed")
    return name

✔ Method name → clean_<fieldname>

---

## Form Level Validation

Multiple fields together validate karne ke liye:

def clean(self):
    data = super().clean()
    p1 = data.get("password")
    p2 = data.get("confirm_password")

    if p1 != p2:
        raise forms.ValidationError("Passwords not match")

✔ Cross-field validation

---

## ModelForm Validation

ModelForm me:

- Model field validators
- clean_<field>()
- clean()

sab run hote hain automatically

---

## Key Points

- form.is_valid() mandatory
- cleaned_data only after valid
- Built-in + custom validation possible
- clean_field() → field level
- clean() → form level

---

## Interview Line

Django form validation happens when is_valid() is called, which runs built-in validators and custom clean methods to ensure correct data.


# Django – CSRF Token Kya Hai Aur Kyu Use Hota Hai?

## CSRF kya hota hai?

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

## Interview Line

CSRF token is a unique security token added to forms to protect Django apps from cross-site request forgery attacks.


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

from django.urls import path
from . import views

urlpatterns = [
    path("home/", views.home),
]

✔ /home/ URL → home view call hogi

---

## Project Level vs App Level urls.py

### Project urls.py
- Main entry router
- Multiple apps ke urls include karta hai

Example:

from django.urls import include, path

path("shop/", include("shop.urls"))

---

### App urls.py
- App specific routes
- Views se direct mapping

---

## Dynamic URL Example

path("product/<int:id>/", views.product_detail)

✔ URL se value pass hoti hai view me

---

## Named URLs

path("home/", views.home, name="home")

Template me:

{% url "home" %}

✔ Hardcoded URL avoid hota hai

---

## Key Points

- URL → View mapping
- Routing system of Django
- Project + app level dono ho sakta hai
- Dynamic parameters support
- Named URLs best practice

---

## Interview Line

urls.py maps URLs to views and acts as the routing system of a Django application.


# Django – path() vs re_path() Difference

## path() kya hai?

`path()` simple aur readable URL patterns define karne ke liye use hota hai.  
Ye Django ka modern aur recommended way hai URL routing ka.

Isme built-in converters use hote hain.

---

## Example

path("product/<int:id>/", views.product_detail)

✔ Easy syntax  
✔ Type converters available  
✔ Most cases me sufficient

---

## Built-in Converters

- <int:id>
- <str:name>
- <slug:slug>
- <uuid:id>
- <path:value>

---

## re_path() kya hai?

`re_path()` regular expressions (regex) based URL matching ke liye use hota hai.

Jab complex pattern matching chahiye ho tab use karte hain.

---

## Example

re_path(r"^product/(?P<id>[0-9]+)/$", views.product_detail)

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

---

## Key Points

- path() → simple & modern
- re_path() → regex support
- New projects me path() prefer karo
- re_path() = advanced routing

---

## Interview Line

path() uses simple readable converters for URLs, while re_path() uses regular expressions for complex URL matching.


# Django – URL Parameters Kaise Lete Hain?

## URL Parameters kya hote hain?

URL parameters wo dynamic values hoti hain jo URL se pass hoti hain
aur view function/class me receive hoti hain.

Ye mostly detail pages, edit pages, filtering, etc. me use hoti hain.

---

## Step 1 – urls.py Me Parameter Define

from django.urls import path
from . import views

urlpatterns = [
    path("product/<int:id>/", views.product_detail),
]

✔ <int:id> → dynamic parameter
✔ Type converter + variable name

---

## Step 2 – View Me Receive

def product_detail(request, id):
    return HttpResponse(id)

✔ Same naam ka argument view me lena hota hai

---

## Multiple Parameters

path("order/<int:oid>/item/<int:iid>/", views.item_view)

def item_view(request, oid, iid):
    pass

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

Namespace URLs ko **group / isolate** karne ka tarika hai  
taaki same name wale URLs different apps me conflict na karein.

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

---

## Interview Lines

URL parameters are captured in urls.py using converters and passed as arguments to views.

Namespace groups URLs under an app label to avoid name conflicts between different apps.


# Django – Django Admin Kya Hai?

## Django Admin kya hai?

Django Admin ek built-in **auto-generated backend panel** hai  
jisse hum database models ka data manage kar sakte hain — bina alag UI banaye.

Isse hum:

- records add
- update
- delete
- search
- filter

sab kar sakte hain.

---

## Django Admin Features

- Model based auto CRUD panel
- Authentication required
- Superuser access
- Search & filters support
- List display customize kar sakte ho
- Forms auto generate hoti hain

---

## Admin URL

/admin/

✔ Project create karte hi available hota hai  
✔ Superuser se login hota hai

---

## Superuser Create

python manage.py createsuperuser

---

## Key Points

- Built-in backend tool
- Fast data management
- No frontend coding needed
- Dev & admin users ke liye best

---

## Interview Line

Django Admin is a built-in interface that allows developers to manage database models through an auto-generated admin panel.

---

# Django – Model Admin Register Kaise Karte Hain?

## Model admin me register kyu karte hain?

Jab tak model admin me register nahi hota,  
wo Django admin panel me visible nahi hota.

Register karne ke baad hi admin UI me manage kar sakte ho.

---

## Step – admin.py Me Register

from django.contrib import admin
from .models import Product

admin.site.register(Product)

✔ Ab Product model admin panel me dikhega

---

## Custom ModelAdmin Class (Important)

from django.contrib import admin
from .models import Product

class ProductAdmin(admin.ModelAdmin):
    list_display = ("id", "name", "price")

admin.site.register(Product, ProductAdmin)

✔ list_display → columns control karta hai

---

## Decorator Style (Shortcut)

@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    list_display = ("name", "price")

✔ Same kaam — cleaner syntax

---

## Key Points

- admin.py file me register hota hai
- admin.site.register(Model)
- Custom ModelAdmin possible
- list_display, search_fields, filters add kar sakte ho

---

## Interview Line

Models are registered in Django admin using admin.site.register() so they can be managed through the admin panel.


# Django Admin – list_display Kya Hota Hai?

## list_display kya hai?

`list_display` Django admin me use hota hai decide karne ke liye ki  
**model list page par kaun-kaun se columns dikhaye jayenge**.

Ye ModelAdmin class ke andar define hota hai.

---

## Important Example

class ProductAdmin(admin.ModelAdmin):
    list_display = ("id", "name", "price", "created_at")

✔ Admin list view me ye columns show honge  
✔ Table view customize hota hai

---

## Notes

- Tuple/list me field names dete hain
- Model ke fields hone chahiye (ya custom methods)
- Readability improve hoti hai

---

## Interview Line

list_display controls which model fields appear as columns in the Django admin list page.

---

# Django Admin – search_fields Ka Use

## search_fields kya karta hai?

`search_fields` admin panel me **search box enable** karta hai  
jisse records ko given fields ke basis par search kar sakte hain.

---

## Important Example

class ProductAdmin(admin.ModelAdmin):
    search_fields = ("name", "description")

✔ Admin me search bar aa jayega  
✔ Name/description ke basis par search karega

---

## Notes

- Text based fields best (CharField, TextField)
- Related fields bhi use kar sakte ho (relation__field)
- Partial matching support hoti hai

---

## Interview Line

search_fields adds a search box in Django admin to search records based on specified fields.

---

# Django Admin – list_filter Kya Karta Hai?

## list_filter kya hota hai?

`list_filter` admin list page me **side filters add** karta hai  
taaki records ko quickly filter kar sakein.

---

## Important Example

class ProductAdmin(admin.ModelAdmin):
    list_filter = ("is_active", "created_at")

✔ Right side filter panel show hota hai  
✔ Click karke data filter kar sakte ho

---

## Notes

- Boolean, Date, Choice, FK fields ke saath useful
- Quick filtering UI milti hai
- Large data sets me helpful

---

## Interview Line

list_filter adds sidebar filters in Django admin to quickly filter records by selected fields.


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

---

## Simple Line

Authentication → Who are you?  
Authorization → What can you do?

---

## Example Flow

1️⃣ User login karta hai → Authentication  
2️⃣ System check karta hai ki edit allowed hai? → Authorization

---

## Key Differences

| Point | Authentication | Authorization |
|--------|----------------|----------------|
Purpose | Identity verify | Permission check |
Stage | First | After auth |
Example | Login | Access control |
Checks | User valid? | Allowed action? |

---

## Interview Line

Authentication verifies user identity, while authorization determines what actions the user is allowed to perform.

---

# Django – Authentication System Kaise Kaam Karta Hai?

## Django Authentication System Overview

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

from django.contrib.auth import authenticate

user = authenticate(request, username=u, password=p)

✔ Username/password verify karta hai  
✔ Valid → user object return  
❌ Invalid → None

---

## Step 2 – Login

from django.contrib.auth import login

login(request, user)

✔ User session create hoti hai  
✔ User logged-in ho jata hai

---

## Step 3 – Access User

request.user

✔ Current logged-in user object  
✔ AnonymousUser if not logged in

---

## Logout

from django.contrib.auth import logout

logout(request)

✔ Session clear  
✔ User logout

---

## Password Security

- Passwords hashed store hote hain
- Plain text store nahi hota
- Django hashers use karta hai

---

## Permissions & Groups

- User permissions assign
- Groups create
- Role based access control

---

## Key Points

- Built-in auth system
- Session based login
- Password hashing default
- request.user everywhere available
- Permissions + groups support

---

## Interview Line

Django authentication works using the built-in User model, authenticate(), login(), and session management to securely verify and manage users.


# Django – @login_required Kya Karta Hai?

## @login_required kya hai?

@login_required ek decorator hai jo ensure karta hai ki  
view sirf **logged-in users** hi access kar saken.

Agar user login nahi hai → login page par redirect ho jayega.

---

## Important Example

from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    return HttpResponse("Dashboard")

✔ Anonymous user → redirect to login  
✔ Authenticated user → view access

---

## Notes

- Views ko protect karta hai
- Login URL settings se control hota hai
- Security best practice

---

## Interview Line

@login_required restricts a view so only authenticated users can access it.

---

# Django – Permissions & Groups Kya Hote Hain?

## Permissions kya hote hain?

Permissions decide karte hain ki user  
**kaunsa action perform kar sakta hai**.

Example:
- add_product
- change_product
- delete_product
- view_product

✔ Model level permissions auto create hoti hain

---

## Groups kya hote hain?

Group = permissions ka collection

Ek group me multiple permissions assign karte hain  
fir users ko group assign kar dete hain.

Example:
Manager group:
- add
- edit
- view allowed

---

## Benefits

- Role-based access
- Easy permission management
- Reusable permission sets
- Admin panel se manage

---

## Interview Line

Permissions control what actions a user can perform, and groups are collections of permissions assigned to multiple users.

---

# Security – CSRF Attack Kya Hai?

## CSRF kya hota hai?

CSRF = Cross-Site Request Forgery

Attack jisme attacker user ke browser se  
**fake authenticated request** bhej deta hai.

User logged-in hota hai → attacker misuse karta hai.

---

## Example

User bank me logged-in  
Attacker hidden form se money transfer request bhej de

---

## Protection

- CSRF token use
- Django CSRF middleware
- {% csrf_token %} in forms

---

## Interview Line

CSRF attack tricks a logged-in user’s browser into sending unwanted authenticated requests.

---

# Security – XSS Kya Hai?

## XSS kya hota hai?

XSS = Cross-Site Scripting

Attack jisme attacker malicious JavaScript code  
website me inject karta hai jo dusre users ke browser me run hota hai.

---

## Example

Comment box me script inject:
<script>alert("hack")</script>

---

## Damage

- Cookie steal
- Session hijack
- Redirects
- Data theft

---

## Django Protection

- Auto HTML escaping
- Template escaping default
- |safe carefully use karo

---

## Interview Line

XSS is an attack where malicious scripts are injected into web pages and executed in users’ browsers.


# Django REST Framework (DRF) – Notes

---

# 63️⃣ Django REST Framework kya hai?

## Definition

Django REST Framework (DRF) Django ka powerful toolkit hai  
jo **RESTful APIs** banane ke liye use hota hai.

Ye Django ke upar built hai aur API development ko fast aur structured banata hai.

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

## Interview Line

DRF is a toolkit built on Django for creating RESTful APIs quickly and securely.

---

# 64️⃣ Serializer kya hota hai?

## Serializer kya karta hai?

Serializer Django model/data ko convert karta hai:

Python object → JSON  
JSON → Python object

✔ API me data send/receive karne ke liye use hota hai

## Example

class ProductSerializer(serializers.Serializer):
    name = serializers.CharField()
    price = serializers.IntegerField()

## Kaam

- Data validation
- Data conversion
- JSON output/input handling

## Interview Line

Serializer converts complex data like models into JSON and validates incoming API data.

---

# 65️⃣ ModelSerializer kya hota hai?

## ModelSerializer kya hai?

ModelSerializer = Serializer ka shortcut version  
jo directly **Django model se fields auto generate** karta hai.

## Example

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = "__all__"

## Benefits

- Less code
- Model based fields auto
- Built-in create/update
- Fast CRUD APIs

## Interview Line

ModelSerializer automatically creates serializer fields from a Django model.

---

# 66️⃣ APIView vs ViewSet Difference

## APIView

- Low-level class
- Har HTTP method manually likhna
- More control
- More code

### Example

class ProductAPI(APIView):
    def get(self, request):
        pass

---

## ViewSet

- High-level abstraction
- CRUD auto handle
- Router ke saath use hota hai
- Less code

### Example

class ProductViewSet(ModelViewSet):
    queryset = Product.objects.all()

---

## Difference Table

| Point | APIView | ViewSet |
|--------|----------|----------|
Level | Low | High |
Code | More | Less |
CRUD | Manual | Auto |
Router | Not needed | Needed |
Control | Full | Structured |

## Interview Line

APIView gives full control with manual methods, while ViewSet provides automatic CRUD with less code.

---

# 67️⃣ Authentication in APIs Kaise Hoti Hai?

## API Authentication Methods (DRF)

### ✅ Token Authentication
Client ko token diya jata hai  
Har request me token send hota hai

Header:
Authorization: Token abc123

---

### ✅ JWT Authentication
Signed token use hota hai  
Stateless auth

---

### ✅ Session Authentication
Browser based login session

---

### ✅ Basic Authentication
Username/password header me

---

## DRF Settings Example

REST_FRAMEWORK = {
  "DEFAULT_AUTHENTICATION_CLASSES": [
    "rest_framework.authentication.TokenAuthentication"
  ]
}

---

## Interview Line

API authentication is done using tokens, JWT, sessions, or basic auth sent with each request.

---

# 68️⃣ Status Codes Examples

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

## DRF Example

from rest_framework.response import Response
from rest_framework import status

return Response(data, status=status.HTTP_201_CREATED)

---

## Interview Line

HTTP status codes indicate the result of an API request like 200 success, 404 not found, 400 bad request, etc.


# Django – Caching, Middleware, Static/Media, Deployment Basics

---

# 69️⃣ Caching kya hoti hai Django me?

## Caching kya hai?

Caching ka matlab hai frequently used data ko temporarily store karna  
taaki har baar database ya heavy processing na karni pade.

✔ Response fast ho jata hai  
✔ Server load kam hota hai

---

## Django Me Cache Types

- Per-site cache → pura site cache
- Per-view cache → specific view cache
- Template fragment cache → template ka part cache
- Low-level cache → manual key-value cache

---

## Per View Example

from django.views.decorators.cache import cache_page

@cache_page(60 * 5)
def home(request):
    pass

✔ 5 min tak cached response milega

---

## Interview Line

Caching stores frequently used data temporarily to improve performance and reduce database load.

---

# 70️⃣ Middleware kya hota hai?

## Middleware kya hai?

Middleware ek layer hoti hai jo  
**request aur response ke beech process** karti hai.

Flow:
Request → Middleware → View → Middleware → Response

---

## Kaam

- Authentication check
- CSRF protection
- Session handling
- Logging
- Security checks

---

## Config Location

settings.py → MIDDLEWARE list

---

## Interview Line

Middleware is a processing layer that runs before and after views to handle request/response tasks.

---

# 71️⃣ Static vs Media Files Difference

## Static Files

Static = fixed files jo developer provide karta hai

Examples:
- CSS
- JS
- Images (design)
- Fonts

✔ Code assets

---

## Media Files

Media = user uploaded files

Examples:
- Profile images
- Documents
- Uploads

✔ Dynamic user data

---

## Settings Example

STATIC_URL = "/static/"
MEDIA_URL = "/media/"

---

## Interview Line

Static files are developer assets (CSS/JS), while media files are user-uploaded content.

---

# 72️⃣ collectstatic command kya karti hai?

## collectstatic kya hai?

Ye command saari static files ko  
**different apps se collect karke ek single folder me copy** karti hai.

Production deployment me use hoti hai.

---

## Command

python manage.py collectstatic

---

## Result

All static → STATIC_ROOT folder me aa jati hain  
Web server yahi se serve karta hai

---

## Interview Line

collectstatic gathers all static files into one directory for production serving.

---

# 73️⃣ Gunicorn / uWSGI kya hai? (Basic Idea)

## Kya hote hain?

Gunicorn aur uWSGI **WSGI servers** hain  
jo Django app ko production me run karte hain.

Browser directly Django se baat nahi karta — WSGI server beech me hota hai.

---

## Role

Client → Nginx → Gunicorn/uWSGI → Django

✔ Request handle  
✔ Workers manage  
✔ Production ready serving

---

## Simple Line

Development → runserver  
Production → Gunicorn / uWSGI

---

## Interview Line

Gunicorn and uWSGI are production WSGI servers used to run Django apps efficiently.

---

# 74️⃣ Environment Variables kyu use karte hain?

## Environment Variables kya hoti hain?

Environment variables external config values hoti hain  
jo code ke bahar store hoti hain.

---

## Kyu Use Karte Hain?

✔ Secrets hide karne ke liye  
✔ Different environments (dev/prod) config  
✔ Security improve  
✔ Hardcoding avoid

---

## Examples

- SECRET_KEY
- DB password
- API keys
- Email credentials

---

## Example Use

import os

SECRET_KEY = os.getenv("SECRET_KEY")

---

## Interview Line

Environment variables store sensitive and environment-specific settings outside the code for security and flexibility.


# Django – Advanced Practical Interview Notes

---

# 75️⃣ Form submit hone ke baad duplicate entry kaise rokoge?

## Problem

User refresh kare → form dubara submit → duplicate record create ❌

---

## Best Solution – POST → Redirect → GET Pattern

Form save karne ke baad redirect karo.

### Example

if form.is_valid():
    form.save()
    return redirect("success_page")

✔ Refresh par duplicate submit nahi hoga

---

## Other Methods

- Unique constraint in model
- get_or_create()
- Token/nonce check
- JavaScript disable submit button

---

## Interview Line

Duplicate form submission is prevented using POST-Redirect-GET pattern and database unique constraints.

---

# 76️⃣ Large QuerySet ko optimize kaise karoge?

## Techniques

### ✅ select_related()
ForeignKey / OneToOne joins optimize

### ✅ prefetch_related()
ManyToMany / reverse relations optimize

### ✅ only() / defer()
Sirf needed fields load karo

### ✅ pagination
Data chunks me load karo

### ✅ values()
Dict result lo instead of model objects

### ✅ iterator()
Memory efficient iteration

---

## Example

Product.objects.select_related("category").only("id","name")

---

## Interview Line

Large querysets are optimized using select_related, prefetch_related, field limiting, and pagination.

---

# 77️⃣ File upload kaise handle karte ho?

## Step 1 – Model Field

file = models.FileField(upload_to="uploads/")

---

## Step 2 – Form

<form method="post" enctype="multipart/form-data">

✔ enctype required

---

## Step 3 – View

form = MyForm(request.POST, request.FILES)

if form.is_valid():
    form.save()

---

## Step 4 – Settings

MEDIA_URL = "/media/"
MEDIA_ROOT = BASE_DIR / "media"

---

## Interview Line

File uploads are handled using FileField, request.FILES, and multipart/form-data encoding.

---

# 78️⃣ Signals kya hote hain Django me?

## Signals kya hain?

Signals Django ka event system hai  
jo allow karta hai ki event hone par automatically function run ho.

---

## Common Signals

- post_save
- pre_save
- post_delete
- pre_delete

---

## Example

from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save, sender=Order)
def after_order(sender, instance, created, **kwargs):
    if created:
        print("Order created")

---

## Use Cases

- Auto profile create
- Email send
- Logs
- Cache clear

---

## Interview Line

Signals trigger automatic actions when model events like save or delete occur.

---

# 79️⃣ Custom User Model kab banana chahiye?

## Kab Banana Chahiye?

✔ Jab extra fields chahiye  
✔ Email as username chahiye  
✔ Login logic change  
✔ Role based user types

---

## Best Practice

Project start me hi custom user model banao  
baad me change karna difficult hota hai.

---

## Method

AbstractUser inherit karke

---

## Interview Line

A custom user model should be created at project start when additional user fields or login behavior is needed.

---

# 80️⃣ transaction.atomic kya hota hai?

## transaction.atomic kya hai?

Ye ensure karta hai ki  
multiple DB operations **all-or-nothing** ho.

✔ Sab success → commit  
❌ Koi fail → rollback

---

## Example

from django.db import transaction

with transaction.atomic():
    create_order()
    charge_payment()
    update_stock()

---

## Use Cases

- Payments
- Multi-step saves
- Critical updates

---

## Interview Line

transaction.atomic ensures database operations run as a single transaction with automatic rollback on failure.


# Django ORM – Advanced Query & DB Concepts Notes

---

# 81️⃣ annotate() – Use Case

## annotate() kya karta hai?

`annotate()` har record ke saath **extra calculated field** add karta hai  
(aggregation per row).

Matlab:
Row-wise computed value add karna.

---

## Important Use Case

Har Author ke saath book count dikhana.

### Example

from django.db.models import Count

authors = Author.objects.annotate(book_count=Count("book"))

✔ Har author object me → book_count field aa jayega

---

## Key Point

annotate() → per object calculation

---

## Interview Line

annotate() adds calculated fields to each row in a queryset using aggregations.

---

# 82️⃣ aggregate() – Use Case

## aggregate() kya karta hai?

`aggregate()` pure queryset par **single summary value** return karta hai.

Matlab:
Overall calculation — not per row.

---

## Important Use Case

Total price nikalna.

### Example

from django.db.models import Sum

total = Order.objects.aggregate(total_price=Sum("price"))

✔ Result → single dict value

---

## Key Point

aggregate() → whole queryset summary

---

## Interview Line

aggregate() returns a single summary value like sum, avg, count over the entire queryset.

---

# 83️⃣ Subquery kya hoti hai?

## Subquery kya hai?

Subquery = ek query jo dusri query ke andar run hoti hai.

✔ Nested query  
✔ Advanced filtering me use

---

## Example Use Case

Un products ko lana jo highest price category me hain.

### Example

from django.db.models import Subquery

latest = Order.objects.filter(user=OuterRef("pk")).values("total")[:1]

User.objects.annotate(last_total=Subquery(latest))

---

## Key Point

Outer query ke andar inner query use hoti hai

---

## Interview Line

A subquery is a query nested inside another query to compute dependent results.

---

# 84️⃣ Database Indexing kya hai?

## Indexing kya hai?

Index database ka **fast lookup structure** hai  
jo search speed improve karta hai.

✔ Faster queries  
✔ Faster filtering

---

## Example

name = models.CharField(max_length=100, db_index=True)

---

## Tradeoff

✔ Read fast  
❌ Write thoda slow  
❌ Extra storage

---

## Interview Line

Database indexing improves query search speed by creating a fast lookup structure on fields.

---

# 85️⃣ unique_together kya hai?

## unique_together kya hai?

Ye ensure karta hai ki  
**multiple fields ka combination unique ho**.

---

## Example

class Meta:
    unique_together = ("user", "product")

✔ Same user + product duplicate nahi ho sakta

---

## Note

Modern Django me UniqueConstraint prefer kiya jata hai.

---

## Interview Line

unique_together enforces uniqueness across a combination of multiple fields.

---

# 86️⃣ Constraints kya hote hain?

## Constraints kya hain?

Constraints database rules hote hain  
jo data validity enforce karte hain.

✔ Invalid data insert hone se rokta hai

---

## Types

- UniqueConstraint
- CheckConstraint
- ForeignKey constraint
- Not null
- Primary key

---

## Example

from django.db.models import Q, CheckConstraint

constraints = [
  CheckConstraint(check=Q(age__gte=18), name="age_18_plus")
]

---

## Key Points

- Data integrity ensure
- DB level protection
- Model Meta me define

---

## Interview Line

Constraints are database-level rules that enforce data validity and integrity.


# Django – Core Runtime & Deployment Concepts Notes

---

# ✅ Django me Middleware hota kya hai?

## Middleware kya hota hai?

Middleware ek processing layer hai jo  
**request aur response ke beech run hoti hai**.

Flow:

Request → Middleware → View → Middleware → Response

---

## Middleware ka Kaam

- Authentication check
- CSRF protection
- Session handling
- Security headers
- Logging
- Caching

---

## Config Location

settings.py me:

MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    ...
]

---

## Interview Line

Middleware is a request–response processing layer that runs before and after Django views.

---

# ✅ Django thread safe hai?

## Answer (Interview Style)

✔ Django mostly thread-safe hai  
❗ But depends on how you write code

---

## Safe Parts

- ORM thread-safe
- Request object per-thread
- DB connections managed

---

## Not Safe If

❌ Global mutable variables use kiye  
❌ Shared state modify kiya  
❌ Non-thread-safe libraries use ki

---

## Best Practice

- No global writable state
- Per-request data hi use karo

---

## Interview Line

Django is thread-safe by design, but developers must avoid shared mutable global state.

---

# ✅ manage.py shell ka use?

## manage.py shell kya hai?

Ye interactive Python shell hai  
jo Django project context ke saath open hota hai.

---

## Command

python manage.py shell

---

## Use Cases

- ORM queries test karna
- Model data check
- Debug logic
- Quick experiments
- Data fix scripts

---

## Example

from shop.models import Product
Product.objects.count()

---

## Interview Line

manage.py shell provides an interactive environment to test Django ORM and project code.

---

# ✅ Migrations rollback kaise karte ho?

## Rollback kya hota hai?

Migration rollback = database ko  
previous migration state me le jana.

---

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

---

## Interview Line

Migrations are rolled back by migrating the app to a previous migration number or to zero.

---

# ✅ debug=True production me kyu nahi?

## debug=True kya karta hai?

Debug mode me Django:

- Detailed error pages show karta hai
- Stack trace show karta hai
- Settings info expose karta hai

---

## Production Risk

❌ Sensitive data leak  
❌ Server paths visible  
❌ Secret keys exposure risk  
❌ Attack surface increase

---

## Production Setting

DEBUG = False

ALLOWED_HOSTS = ["yourdomain.com"]

---

## Interview Line

debug=True should not be used in production because it exposes sensitive error and system information.



```python

```
