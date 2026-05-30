# Django – Migrations & Model Advanced Concepts Notes

---

# 1️⃣ Django migrations rollback kaise karte ho?

## Rollback Meaning?

Rollback = DB schema ko previous migration state me wapas le jana.

---

## Specific Migration Tak Rollback

python manage.py migrate app_name 0003

✔ App migration 0003 tak revert ho jayegi

---

## Full Rollback

python manage.py migrate app_name zero

✔ App ki sab migrations undo

---

## Migration Status Check

python manage.py showmigrations

---

## Interview Line

Migrations are rolled back by migrating to a previous migration number or to zero.

---

# 2️⃣ Fake Migration kya hoti hai?

## Fake Migration kya hai?

Fake migration me Django migration ko **applied mark kar deta hai**  
without actually running DB changes.

---

## Command

python manage.py migrate --fake

---

## Use Case

- DB manually updated ho
- Schema already match karta ho
- Legacy database sync

---

## Interview Line

Fake migration marks a migration as applied without executing database operations.

---

# 3️⃣ Data Migration kya hoti hai?

## Data Migration kya hai?

Data migration schema nahi — **existing data ko modify** karti hai.

✔ Data transform  
✔ Field values update  
✔ Default fill

---

## Example Use

- New field add → old rows fill
- Status rename
- Data cleanup

---

## Interview Line

Data migration modifies existing database records instead of schema.

---

# 4️⃣ RunPython Migration kya hoti hai?

## RunPython kya hai?

RunPython migration operation hai  
jo migration ke andar Python code run karne deta hai.

---

## Example

from django.db import migrations

def fill_data(apps, schema_editor):
    Model = apps.get_model("shop", "Product")
    Model.objects.update(active=True)

operations = [
    migrations.RunPython(fill_data),
]

---

## Use Case

- Bulk updates
- Default data insert
- Data transform

---

## Interview Line

RunPython allows executing custom Python code inside migrations for data changes.

---

# 5️⃣ Proxy Model kya hota hai?

## Proxy Model kya hai?

Proxy model same DB table use karta hai  
but **different Python behavior** deta hai.

✔ No new table  
✔ Behavior change only

---

## Example

class ActiveUser(User):
    class Meta:
        proxy = True

---

## Use Case

- Custom manager
- Different ordering
- Admin customization

---

## Interview Line

A proxy model changes Python behavior without creating a new database table.

---

# 6️⃣ Abstract Model kya hota hai?

## Abstract Model kya hai?

Abstract model base class hoti hai  
jiska **khud ka table nahi banta**.

Fields child models me copy ho jate hain.

---

## Example

class BaseModel(models.Model):
    created_at = models.DateTimeField()

    class Meta:
        abstract = True

---

## Use Case

- Shared fields
- DRY models

---

## Interview Line

Abstract models provide reusable fields without creating a database table.

---

# 7️⃣ Multi-table inheritance kya hoti hai?

## Multi-table inheritance kya hai?

Har model ka **separate table** banta hai  
parent-child relation ke saath.

---

## Example

class Person(models.Model):
    name = models.CharField(max_length=50)

class Employee(Person):
    salary = models.IntegerField()

✔ 2 tables create hongi

---

## Interview Line

Multi-table inheritance creates a separate database table for each model in the hierarchy.

---

# 8️⃣ db_index ka use kya hai?

## db_index kya karta hai?

Field par database index create karta hai  
taaki search/filter fast ho.

---

## Example

email = models.EmailField(db_index=True)

---

## Tradeoff

✔ Fast reads  
❌ Slower writes  
❌ Extra storage

---

## Interview Line

db_index creates a database index to speed up queries on a field.

---

# 9️⃣ unique=True vs UniqueConstraint difference?

## unique=True

- Single field unique
- Field level
- Simple case

Example:
email = models.EmailField(unique=True)

---

## UniqueConstraint

- Multi-field unique
- Meta level
- Advanced control

Example:

constraints = [
    models.UniqueConstraint(
        fields=["user","product"],
        name="unique_user_product"
    )
]

---

## Interview Line

unique=True applies to one field, while UniqueConstraint supports multi-field uniqueness rules.

---

# 🔟 AutoField vs BigAutoField

## AutoField

- Integer auto increment PK
- Smaller range

---

## BigAutoField

- Big integer auto increment PK
- Large range
- New Django default

---

## Difference

AutoField → ~2B range  
BigAutoField → very large range

---

## Interview Line

AutoField is a standard integer auto-increment key, while BigAutoField supports a much larger ID range.


# Django ORM – Advanced Query Examples & Concepts

---

# 11️⃣ Subquery ka Example

## Subquery kya hai?

Subquery = ek query jo dusri query ke andar use hoti hai  
taaki dependent value fetch ho sake.

---

## Example – Latest Order Total per User

from django.db.models import Subquery, OuterRef

latest_total = Order.objects.filter(
    user=OuterRef("pk")
).order_by("-id").values("total")[:1]

users = User.objects.annotate(
    last_order_total=Subquery(latest_total)
)

✔ Har user ke saath latest order total attach

---

## Interview Line

Subquery allows using one queryset inside another to compute dependent values.

---

# 12️⃣ Exists Query kya hoti hai?

## Exists kya karta hai?

Exists check karta hai ki related record **exist karta hai ya nahi**  
boolean result deta hai.

---

## Example

from django.db.models import Exists, OuterRef

orders = Order.objects.filter(user=OuterRef("pk"))

users = User.objects.annotate(
    has_orders=Exists(orders)
)

✔ True/False flag milega

---

## Interview Line

Exists is used to check whether related rows exist using a fast boolean subquery.

---

# 13️⃣ OuterRef kya hota hai?

## OuterRef kya hai?

OuterRef outer query ke field ko  
inner subquery ke andar reference karne deta hai.

---

## Example Idea

user=OuterRef("pk")

✔ Subquery ko batata hai → outer row ka pk use karo

---

## Interview Line

OuterRef lets a subquery reference fields from the outer query.

---

# 14️⃣ Raw SQL Django me kaise chalate ho?

## Method 1 – raw()

qs = Product.objects.raw(
    "SELECT * FROM shop_product WHERE price > %s",
    [100]
)

---

## Method 2 – cursor

from django.db import connection

with connection.cursor() as c:
    c.execute("SELECT COUNT(*) FROM shop_product")
    row = c.fetchone()

---

## Use Case

- Complex queries
- DB specific features
- Performance tuning

---

## Interview Line

Raw SQL can be executed using Model.objects.raw() or database cursors.

---

# 15️⃣ .only() vs .defer() Difference

## only()

Sirf given fields load karega  
baaki deferred

Product.objects.only("id","name")

---

## defer()

Given fields skip karega  
baaki sab load

Product.objects.defer("description")

---

## Difference

only → include few  
defer → exclude few

---

## Interview Line

only() loads specified fields, while defer() skips specified fields.

---

# 16️⃣ select_for_update kya karta hai?

## select_for_update kya hai?

Row-level DB lock lagata hai  
transaction ke andar.

✔ Prevent concurrent updates

---

## Example

from django.db import transaction

with transaction.atomic():
    p = Product.objects.select_for_update().get(id=1)
    p.stock -= 1
    p.save()

---

## Use Case

- Payments
- Inventory updates
- Critical counters

---

## Interview Line

select_for_update locks selected rows until the transaction completes.

---

# 17️⃣ distinct() kab use karte ho?

## distinct() kya karta hai?

Duplicate rows remove karta hai.

---

## Example

Product.objects.values("category").distinct()

✔ Unique categories

---

## Use Case

- Join duplicates remove
- Unique result sets

---

## Interview Line

distinct() removes duplicate rows from queryset results.

---

# 18️⃣ union() queryset kya hota hai?

## union() kya karta hai?

Do querysets ko combine karta hai  
SQL UNION jaisa.

---

## Example

qs = qs1.union(qs2)

✔ Same columns required

---

## Interview Line

union() combines multiple querysets into one result set.

---

# 19️⃣ Chaining queries kaise kaam karti hain?

## Query Chaining

QuerySet methods chain ho sakti hain  
aur final query ek hi banti hai.

---

## Example

qs = Product.objects.filter(active=True)\
        .exclude(price=0)\
        .order_by("name")

✔ Single SQL query banegi

---

## Benefit

- Lazy build
- Optimized execution

---

## Interview Line

Queryset chaining builds a single optimized query through method chaining.

---

# 20️⃣ QuerySet evaluate kab hota hai?

## Lazy Evaluation

QuerySet tab tak DB hit nahi karta  
jab tak data actually chahiye na ho.

---

## Evaluate Kab Hota Hai?

- loop karte time
- list() conversion
- slicing
- count()
- exists()
- first()
- template use
- bool(qs)

---

## Example

qs = Product.objects.all()   ❌ no query yet
list(qs)                     ✔ query runs

---

## Interview Line

A queryset is evaluated only when its data is actually accessed.


# Django – Request Flow, Middleware, CBV & Pagination Notes

---

# 21️⃣ Django Request Lifecycle

## Request Lifecycle Flow

Browser → Django Server → Response

Detailed flow:

1️⃣ Client request bhejta hai  
2️⃣ Web server (Gunicorn/uWSGI) receive karta hai  
3️⃣ Django URL resolver URL match karta hai  
4️⃣ Middleware (request phase) run hoti hai  
5️⃣ View execute hoti hai  
6️⃣ Template render hota hai (agar needed)  
7️⃣ Middleware (response phase) run hoti hai  
8️⃣ Response client ko return

---

## Interview Line

Django request lifecycle goes through URL routing → middleware → view → template → response middleware → client.

---

# 22️⃣ Middleware Execution Order

## Order Rule

Middleware **top to bottom** request me run hoti hai  
aur **bottom to top** response me.

---

## Example

MIDDLEWARE = [A, B, C]

Request flow:
A → B → C → View

Response flow:
View → C → B → A

---

## Interview Line

Middleware executes top-down for requests and bottom-up for responses.

---

# 23️⃣ Custom Middleware Kaise Likhte Ho?

## Basic Structure

class MyMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # before view
        response = self.get_response(request)
        # after view
        return response

---

## Register in settings.py

MIDDLEWARE += ["myapp.middleware.MyMiddleware"]

---

## Use Cases

- Logging
- Timing
- Custom headers
- Access checks

---

## Interview Line

Custom middleware is written as a callable class and added to the MIDDLEWARE setting.

---

# 24️⃣ Decorators vs Middleware Difference

## Decorators

- View level
- Specific view par apply
- Function/Class wrap karta hai

Example:
@login_required

---

## Middleware

- Global level
- Har request par apply
- Request/response pipeline me

---

## Difference

Decorator → per view  
Middleware → whole project

---

## Interview Line

Decorators apply to specific views, while middleware applies globally to all requests.

---

# 25️⃣ Mixins kya hote hain CBV me?

## Mixins kya hain?

Mixins reusable class pieces hain  
jo CBV me extra functionality add karte hain.

---

## Example Mixins

- LoginRequiredMixin
- PermissionRequiredMixin

---

## Benefit

- Code reuse
- Multiple inheritance
- Feature add without rewrite

---

## Interview Line

Mixins are reusable classes that add specific behavior to class-based views.

---

# 26️⃣ dispatch() Method kya karta hai?

## dispatch() kya hai?

dispatch() decide karta hai  
kaunsa method call hoga — get/post/put/delete

---

## Flow

dispatch() → method detect → get()/post() call

---

## Override Use Case

- Pre-check logic
- Permission checks
- Logging

---

## Interview Line

dispatch() routes a request to the correct HTTP method handler in a CBV.

---

# 27️⃣ get_queryset override kyu karte hain?

## Purpose

Default queryset ko customize karne ke liye.

---

## Example

def get_queryset(self):
    return Product.objects.filter(active=True)

---

## Use Cases

- User-based filtering
- Dynamic filters
- Permission based data

---

## Interview Line

get_queryset is overridden to dynamically customize the data returned by a CBV.

---

# 28️⃣ get_context_data override kyu karte hain?

## Purpose

Template context me extra data bhejne ke liye.

---

## Example

def get_context_data(self, **kwargs):
    ctx = super().get_context_data(**kwargs)
    ctx["total"] = Product.objects.count()
    return ctx

---

## Interview Line

get_context_data is overridden to add extra variables to the template context.

---

# 29️⃣ Slug Based Views Kaise Banate Ho?

## Step 1 – Model

slug = models.SlugField(unique=True)

---

## Step 2 – URL

path("post/<slug:slug>/", views.PostDetail.as_view())

---

## Step 3 – CBV Setting

slug_field = "slug"
slug_url_kwarg = "slug"

---

## Benefit

- SEO friendly URLs
- Readable links

---

## Interview Line

Slug-based views use SlugField and slug URL converters for SEO-friendly URLs.

---

# 30️⃣ Pagination Kaise Implement Karte Ho?

## Method 1 – Paginator Class

from django.core.paginator import Paginator

p = Paginator(qs, 10)
page = p.get_page(request.GET.get("page"))

---

## Method 2 – CBV

class ProductList(ListView):
    model = Product
    paginate_by = 10

---

## Benefit

- Large data split
- Performance improve
- Better UX

---

## Interview Line

Pagination splits large querysets into pages using Paginator or paginate_by in CBVs.


# Django – Templates & Forms Advanced Notes

---

# 31️⃣ Custom Template Tag Kaise Banate Ho?

## Custom Template Tag kya hai?

Custom template tag = apna khud ka template function  
jo template me logic run karne deta hai.

---

## Steps

1️⃣ app me folder banao → templatetags  
2️⃣ __init__.py add karo  
3️⃣ file banao → custom_tags.py  
4️⃣ register karo

---

## Example

from django import template
register = template.Library()

@register.simple_tag
def site_name():
    return "My Site"

---

## Template Use

{% load custom_tags %}
{% site_name %}

---

## Interview Line

Custom template tags are created using template.Library() and registered with decorators.

---

# 32️⃣ Inclusion Tag kya hota hai?

## Inclusion Tag kya hai?

Inclusion tag ek custom tag hai  
jo **separate template render** karta hai.

---

## Example

@register.inclusion_tag("menu.html")
def show_menu():
    return {"items": Menu.objects.all()}

---

## Use

{% show_menu %}

✔ menu.html render hoga

---

## Interview Line

Inclusion tags render a separate template with provided context data.

---

# 33️⃣ Template Context Processors kya hote hain?

## Context Processor kya hai?

Function jo **har template context me auto data add** karta hai.

---

## Example

def site_settings(request):
    return {"SITE_NAME": "MySite"}

---

## Register

settings.py → TEMPLATES → context_processors

---

## Use

{{ SITE_NAME }}

---

## Interview Line

Context processors automatically add variables to all template contexts.

---

# 34️⃣ Safe Filter kya karta hai?

## safe filter kya karta hai?

safe filter HTML escaping disable karta hai  
aur raw HTML render karne deta hai.

---

## Example

{{ html_content|safe }}

---

## Warning

❗ XSS risk agar user input ho

---

## Interview Line

safe filter disables auto-escaping and renders HTML as-is.

---

# 35️⃣ autoescape kya hota hai?

## autoescape kya hai?

Django templates by default  
HTML escape karte hain for security.

---

## Disable Block

{% autoescape off %}
{{ data }}
{% endautoescape %}

---

## Interview Line

Autoescape automatically escapes HTML in templates to prevent XSS.

---

# 36️⃣ clean() vs clean_fieldname() Difference

## clean_fieldname()

- Single field validation
- Field specific

Example:
clean_email()

---

## clean()

- Full form validation
- Cross-field checks

---

## Difference

clean_field → field level  
clean → form level

---

## Interview Line

clean_fieldname validates one field, while clean validates the whole form.

---

# 37️⃣ Custom Validator Kaise Likhte Ho?

## Example

from django.core.exceptions import ValidationError

def even_validator(v):
    if v % 2 != 0:
        raise ValidationError("Must be even")

---

## Use

age = models.IntegerField(validators=[even_validator])

---

## Interview Line

Custom validators are functions that raise ValidationError when rules fail.

---

# 38️⃣ Formset kya hota hai?

## Formset kya hai?

Formset = same form ke **multiple instances** ek saath handle karna.

---

## Example

from django.forms import formset_factory

MySet = formset_factory(MyForm, extra=3)

---

## Use Case

- Multiple rows input
- Bulk data entry

---

## Interview Line

Formset manages multiple copies of the same form in one request.

---

# 39️⃣ Inline Formset kya hota hai?

## Inline Formset kya hai?

Parent-child models ke liye formset  
(ForeignKey relation required).

---

## Example

from django.forms import inlineformset_factory

BookSet = inlineformset_factory(Author, Book)

---

## Use Case

Author + multiple books edit

---

## Interview Line

Inline formsets manage related child objects linked by ForeignKey.

---

# 40️⃣ Multiple Forms Ek Page Par Kaise Handle Karte Ho?

## Method

Har form ka prefix use karo.

---

## Example

form1 = AForm(request.POST, prefix="a")
form2 = BForm(request.POST, prefix="b")

---

## Template

{{ form1 }}
{{ form2 }}

---

## Validate

if form1.is_valid() and form2.is_valid():

---

## Interview Line

Multiple forms are handled using prefixes to avoid field name conflicts.


# Django – Files, Storage, Signals, Auth Advanced Notes

---

# 41️⃣ FileField vs ImageField Difference

## FileField

- Any file type allow
- No image validation

Example:
file = models.FileField(upload_to="docs/")

---

## ImageField

- Sirf images allow
- Pillow library required
- Image validation built-in

Example:
image = models.ImageField(upload_to="images/")

---

## Interview Line

FileField stores any file, while ImageField validates that the file is an image.

---

# 42️⃣ MEDIA_ROOT vs STATIC_ROOT Difference

## MEDIA_ROOT

- User uploaded files location
- Runtime files

Example:
MEDIA_ROOT = BASE_DIR / "media"

---

## STATIC_ROOT

- Collected static files location
- collectstatic output

Example:
STATIC_ROOT = BASE_DIR / "staticfiles"

---

## Interview Line

MEDIA_ROOT stores user uploads, STATIC_ROOT stores collected static assets for production.

---

# 43️⃣ File Upload Size Limit Kaise Lagate Ho?

## Method 1 – Settings

DATA_UPLOAD_MAX_MEMORY_SIZE

---

## Method 2 – Validator

def size_limit(f):
    if f.size > 2*1024*1024:
        raise ValidationError("Too large")

---

## Method 3 – Web server limit

Nginx / Apache config

---

## Interview Line

Upload size can be limited via settings, validators, or web server configuration.

---

# 44️⃣ S3 Storage Django Me Kaise Use Karte Ho? (Basic)

## Basic Idea

Django files ko local disk ke bajay  
AWS S3 bucket me store karta hai.

---

## Steps

- django-storages install
- boto3 install
- S3 credentials config
- DEFAULT_FILE_STORAGE set

---

## Setting Idea

DEFAULT_FILE_STORAGE = "storages.backends.s3boto3.S3Boto3Storage"

---

## Interview Line

S3 storage is used via django-storages to store media files in AWS S3 instead of local disk.

---

# 45️⃣ User Uploaded File Rename Kaise Karte Ho?

## Method – upload_to Function

def path_func(instance, filename):
    return f"user_{instance.user_id}/{filename}"

file = models.FileField(upload_to=path_func)

---

## Interview Line

Files are renamed using a callable upload_to function in FileField.

---

# 46️⃣ Django Signals kya hote hain?

## Signals kya hain?

Signals event hooks hain  
jo model events par auto code run karte hain.

---

## Examples

- post_save
- pre_save
- post_delete

---

## Interview Line

Signals trigger automatic actions on model lifecycle events.

---

# 47️⃣ post_save Signal Use Case

## Example Use

- Profile auto create
- Email send
- Cache clear

---

## Example

@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kw):
    if created:
        Profile.objects.create(user=instance)

---

## Interview Line

post_save runs after a model is saved and is used for follow-up actions.

---

# 48️⃣ Signals vs Overriding save() Difference

## save() override

- Model internal logic
- Direct & visible

---

## Signals

- External hook
- Loose coupling

---

## Difference

save() → tight coupling  
signal → decoupled

---

## Interview Line

save() override embeds logic in model, signals keep logic separate.

---

# 49️⃣ Signals ke Drawbacks

## Problems

- Hidden logic
- Hard debugging
- Order issues
- Multiple triggers risk
- Performance impact

---

## Interview Line

Signals can make code harder to trace and debug due to hidden side effects.

---

# 50️⃣ Custom User Model Kaise Banate Ho?

## Step

AbstractUser inherit karo

---

## Example

class User(AbstractUser):
    phone = models.CharField(max_length=20)

---

## Setting

AUTH_USER_MODEL = "accounts.User"

---

## Interview Line

Custom user models are created by extending AbstractUser and setting AUTH_USER_MODEL.

---

# 51️⃣ AbstractUser vs AbstractBaseUser

## AbstractUser

- Ready fields
- Quick setup
- Recommended

---

## AbstractBaseUser

- Minimal base
- Full custom auth
- More work

---

## Interview Line

AbstractUser is full-featured, AbstractBaseUser is low-level and fully customizable.

---

# 52️⃣ Password Hashing Kaise Hoti Hai?

## Django Method

Passwords hashed store hote hain  
plain text nahi.

---

## Functions

set_password()
check_password()

---

## Algorithm

PBKDF2 (default)

---

## Interview Line

Django hashes passwords using PBKDF2 and never stores plain text passwords.

---

# 53️⃣ Permission Required Decorator kya hai?

## Purpose

View access restrict based on permission.

---

## Example

from django.contrib.auth.decorators import permission_required

@permission_required("shop.add_product")

---

## Interview Line

permission_required restricts a view to users with specific permissions.

---

# 54️⃣ Object Level Permissions kya hote hain?

## Meaning

Permission per object basis  
not model level.

---

## Example

User can edit only his own document.

---

## Tools

django-guardian library

---

## Interview Line

Object-level permissions control access to specific model instances instead of entire models.


# Django REST Framework – Serializer & API Security Notes

---

# 55️⃣ Serializer Validation Kaise Likhte Ho?

## Field Level Validation

Method name → validate_<field>()

Example:

class UserSer(serializers.Serializer):
    age = serializers.IntegerField()

    def validate_age(self, v):
        if v < 18:
            raise serializers.ValidationError("Too young")
        return v

---

## Object Level Validation

Multiple fields validate

def validate(self, data):
    if data["p1"] != data["p2"]:
        raise serializers.ValidationError("Mismatch")
    return data

---

## Interview Line

Serializer validation is written using validate_fieldname and validate methods.

---

# 56️⃣ write_only vs read_only Fields

## write_only

- Input allowed
- Output me show nahi hota

Example:
password = serializers.CharField(write_only=True)

---

## read_only

- Output me show
- Input accept nahi

Example:
id = serializers.IntegerField(read_only=True)

---

## Interview Line

write_only fields accept input but are not returned, read_only fields are returned but not accepted as input.

---

# 57️⃣ Nested Serializer kya hota hai?

## Nested Serializer

Serializer ke andar dusra serializer use karna.

---

## Example

class BookSer(serializers.ModelSerializer):
    class Meta: ...

class AuthorSer(serializers.ModelSerializer):
    books = BookSer(many=True)

---

## Use Case

- Related objects show
- Joined API output

---

## Interview Line

Nested serializers include related object data inside parent serializer output.

---

# 58️⃣ Pagination DRF me kaise?

## Global Setting

REST_FRAMEWORK = {
 "DEFAULT_PAGINATION_CLASS":
  "rest_framework.pagination.PageNumberPagination",
 "PAGE_SIZE": 10
}

---

## View Level

pagination_class = PageNumberPagination

---

## Types

- PageNumber
- LimitOffset
- Cursor

---

## Interview Line

DRF pagination is enabled via pagination classes like PageNumberPagination.

---

# 59️⃣ Throttling kya hoti hai?

## Throttling kya hai?

API request rate limit system  
jo abuse prevent karta hai.

---

## Example

100 requests per hour per user

---

## Setting

DEFAULT_THROTTLE_CLASSES

---

## Interview Line

Throttling limits how many API requests a client can make in a time period.

---

# 60️⃣ Token vs JWT Difference

## Token Auth

- DB stored token
- Simple
- Stateful

---

## JWT

- Self-contained token
- Signed payload
- Stateless
- No DB lookup needed

---

## Difference

Token → DB check  
JWT → signature verify

---

## Interview Line

Token auth uses stored tokens, JWT uses signed stateless tokens.

---

# 61️⃣ SerializerMethodField kya hai?

## SerializerMethodField kya hai?

Custom computed field  
serializer method se value aati hai.

---

## Example

class UserSer(serializers.ModelSerializer):
    full = serializers.SerializerMethodField()

    def get_full(self,obj):
        return obj.first + obj.last

---

## Interview Line

SerializerMethodField returns a computed value using a serializer method.

---

# 62️⃣ Custom Permission Class Kaise Likhte Ho?

## Example

from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):
    def has_object_permission(self, req, view, obj):
        return obj.user == req.user

---

## Use

permission_classes = [IsOwner]

---

## Interview Line

Custom permissions are created by extending BasePermission and overriding permission methods.


# Django – Caching, Performance, Celery & Background Tasks Notes

---

# 63️⃣ Django Caching Types

## Main Cache Types

### ✅ Per-site cache
Puri website responses cache

### ✅ Per-view cache
Specific view response cache

### ✅ Template fragment cache
Template ka ek part cache

### ✅ Low-level cache
Manual key-value caching

---

## Interview Line

Django supports per-site, per-view, template fragment, and low-level caching.

---

# 64️⃣ Per-view Cache Kaise Lagate Ho?

## Decorator Method

from django.views.decorators.cache import cache_page

@cache_page(60 * 10)
def home(request):
    pass

✔ 10 minutes cache

---

## CBV Method

from django.utils.decorators import method_decorator

@method_decorator(cache_page(600), name="dispatch")

---

## Interview Line

Per-view caching is applied using the cache_page decorator.

---

# 65️⃣ Template Fragment Caching kya hai?

## Meaning

Template ke ek specific block ko cache karna.

---

## Example

{% load cache %}

{% cache 300 sidebar %}
   heavy block
{% endcache %}

---

## Use Case

- Sidebar
- Menus
- Widgets

---

## Interview Line

Template fragment caching caches only a specific template block.

---

# 66️⃣ Redis kyu use karte hain?

## Redis kya hai?

In-memory data store  
super fast key-value DB.

---

## Django Me Use

- Cache backend
- Celery broker
- Sessions
- Rate limiting

---

## Benefit

✔ Very fast  
✔ In-memory  
✔ Scalable

---

## Interview Line

Redis is used for fast in-memory caching and background task brokering.

---

# 67️⃣ Query Optimization Steps

## Steps

- select_related()
- prefetch_related()
- only()/defer()
- indexes add
- pagination
- avoid N+1
- values() use
- annotate instead loops

---

## Interview Line

Queries are optimized using joins, field limiting, indexing, and prefetching.

---

# 68️⃣ Debug Toolbar Use Kiya Hai?

## Django Debug Toolbar

Dev tool jo show karta hai:

- SQL queries
- Time taken
- Cache hits
- Headers
- Templates

---

## Use

Performance debugging

---

## Interview Line

Django Debug Toolbar is used to inspect queries and performance in development.

---

# 69️⃣ Celery kya hota hai?

## Celery kya hai?

Distributed task queue system  
background tasks run karta hai.

---

## Use

- Emails
- Reports
- Image processing
- Heavy jobs

---

## Interview Line

Celery runs background asynchronous tasks outside request cycle.

---

# 70️⃣ Celery + Redis Use Case

## Flow

App → Redis (broker) → Celery worker → task run

---

## Use Case

- Background email
- File processing
- Notifications

---

## Interview Line

Redis is used as broker and result backend for Celery tasks.

---

# 71️⃣ Background Email Sending Kaise Karte Ho?

## Method

Celery task use karo

---

## Example Idea

@shared_task
def send_mail_task():
    send_mail(...)

---

## Benefit

✔ Non-blocking  
✔ Fast response

---

## Interview Line

Background emails are sent using Celery tasks.

---

# 72️⃣ Scheduled Tasks Kaise Chalate Ho?

## Method 1 – Celery Beat

Scheduler for periodic tasks

---

## Method 2 – Cron job

System level scheduling

---

## Method 3 – APScheduler

Python scheduler

---

## Interview Line

Scheduled tasks run using Celery Beat or cron jobs.


# Django – Testing, Production & Deployment Notes

---

# 73️⃣ Django TestCase kya hota hai?

## Django TestCase kya hai?

Django TestCase ek testing class hai  
jo Django apps ke **unit tests** likhne ke liye use hoti hai.

---

## Features

- Test DB auto create
- Har test isolated
- Rollback after test
- ORM ready

---

## Example

from django.test import TestCase

class ProductTest(TestCase):
    def test_create(self):
        Product.objects.create(name="A")
        self.assertEqual(Product.objects.count(), 1)

---

## Interview Line

Django TestCase is used to write unit tests with an isolated test database.

---

# 74️⃣ Client Test kya hota hai?

## Django Test Client

Test client fake browser ki tarah behave karta hai  
aur views ko hit karta hai.

---

## Example

from django.test import Client

c = Client()
resp = c.get("/home/")

---

## Use

- URL test
- Response test
- Login simulate

---

## Interview Line

Django test client simulates HTTP requests for testing views.

---

# 75️⃣ Factory vs Fixture Difference

## Fixture

- Static test data
- JSON/YAML files
- Predefined records

---

## Factory (factory_boy)

- Dynamic test data
- Python based
- Flexible

---

## Difference

Fixture → fixed  
Factory → dynamic

---

## Interview Line

Fixtures provide static test data, factories generate dynamic test objects.

---

# 76️⃣ Mocking kya hoti hai?

## Mocking kya hai?

External dependency ko fake object se replace karna  
testing ke time.

---

## Example Use

- API call mock
- Email send mock
- Payment gateway mock

---

## Tool

unittest.mock

---

## Interview Line

Mocking replaces real dependencies with fake ones during tests.

---

# 77️⃣ API Testing Kaise Karte Ho?

## Tools

- DRF APIClient
- pytest
- Postman
- curl

---

## DRF Example

from rest_framework.test import APIClient

client = APIClient()
r = client.get("/api/products/")

---

## Interview Line

API testing is done using DRF APIClient, pytest, or tools like Postman.

---

# 78️⃣ Django Production Settings kya change karte ho?

## Important Changes

- DEBUG = False
- Proper DB config
- Static/media config
- Cache enable
- Secure cookies
- Logging enable
- Allowed hosts set

---

## Interview Line

Production settings disable debug and enable security, caching, and proper hosts.

---

# 79️⃣ ALLOWED_HOSTS kya hai?

## ALLOWED_HOSTS kya hai?

List of domains/IPs  
jinse app request accept karega.

---

## Example

ALLOWED_HOSTS = ["example.com"]

---

## Interview Line

ALLOWED_HOSTS defines which domains can serve the Django app.

---

# 80️⃣ DEBUG False kyu?

## Reason

Debug pages sensitive info dikhate hain.

---

## Risk

- Stack trace leak
- Paths visible
- Settings exposure

---

## Interview Line

DEBUG is set False in production to prevent sensitive data exposure.

---

# 81️⃣ Secret Key hide kaise karte ho?

## Method

Environment variables me store

---

## Example

SECRET_KEY = os.getenv("SECRET_KEY")

---

## Interview Line

Secret keys are hidden using environment variables.

---

# 82️⃣ Env Variables kaise use karte ho?

## Example

import os

DB_PASS = os.getenv("DB_PASS")

---

## Tools

- python-dotenv
- django-environ

---

## Interview Line

Environment variables are accessed using os.getenv for secure config.

---

# 83️⃣ Docker use kiya hai Django me?

## Docker Role

App ko container me run karna  
consistent environment.

---

## Benefits

- Same env everywhere
- Easy deploy
- Dependency control

---

## Interview Line

Docker containers package Django with dependencies for consistent deployment.

---

# 84️⃣ Nginx role kya hai?

## Nginx kya karta hai?

Reverse proxy + static server

---

## Flow

Client → Nginx → Gunicorn → Django

---

## Kaam

- Static files serve
- SSL terminate
- Load balancing
- Reverse proxy

---

## Interview Line

Nginx acts as reverse proxy and static file server in Django deployment.


# Django – Performance, Concurrency, Architecture & Async Notes

---

# 85️⃣ Agar Site Slow Ho Jaye To Kya Check Karoge?

## Checklist

- Slow DB queries (N+1 issue)
- Missing indexes
- Heavy queryset load
- Debug toolbar query count
- Caching enabled?
- Static/media serving ok?
- CPU / memory usage
- External API delay
- Blocking tasks in views

---

## Interview Line

Check DB queries, indexing, caching, heavy logic, and external calls first.

---

# 86️⃣ Duplicate Form Submission Kaise Rokenge?

## Best Method – PRG Pattern

POST → Redirect → GET

if form.is_valid():
    form.save()
    return redirect("success")

---

## Other Methods

- Unique constraints
- Tokens
- Disable submit button
- get_or_create()

---

## Interview Line

Use POST-Redirect-GET and unique constraints to prevent duplicates.

---

# 87️⃣ Race Condition Kaise Handle Karoge?

## Methods

- transaction.atomic
- select_for_update
- DB constraints
- Optimistic locking
- Idempotent logic

---

## Interview Line

Race conditions are handled using DB locks and atomic transactions.

---

# 88️⃣ Concurrent Update Problem Ka Solution?

## Method – Row Lock

select_for_update()

with transaction.atomic():
    obj = Model.objects.select_for_update().get(id=1)

---

## Alternative

Version field check (optimistic lock)

---

## Interview Line

Concurrent updates are solved using row-level locks or version checks.

---

# 89️⃣ transaction.atomic Use Case

## Use Case

Multiple DB writes must succeed together.

---

## Example

with transaction.atomic():
    create_order()
    charge()
    reduce_stock()

---

## Interview Line

transaction.atomic ensures all DB operations succeed or rollback together.

---

# 90️⃣ Soft Delete Kaise Implement Karoge?

## Method

Add flag field

is_deleted = models.BooleanField(default=False)

---

## Manager Filter

def get_queryset():
    return super().get_queryset().filter(is_deleted=False)

---

## Interview Line

Soft delete uses a flag field instead of deleting rows.

---

# 91️⃣ Audit Log Kaise Maintain Karoge?

## Methods

- Signals
- save() override
- Audit model table
- django-simple-history

---

## Store

- user
- action
- timestamp
- old/new values

---

## Interview Line

Audit logs are maintained using signals or audit models.

---

# 92️⃣ Multi-tenant App Kaise Design Karoge?

## Approaches

### Shared DB + tenant_id
Row level isolation

### Separate schema per tenant
Schema isolation

### Separate DB per tenant
Full isolation

---

## Tools

django-tenants

---

## Interview Line

Multi-tenant apps isolate tenant data using tenant_id, schemas, or separate DBs.

---

# 93️⃣ Django Thread Safe Hai?

## Answer

✔ Mostly yes  
❗ Avoid shared mutable globals

---

## Safe

- ORM
- request objects

---

## Interview Line

Django is thread-safe if no shared mutable global state is used.

---

# 94️⃣ WSGI vs ASGI Difference

## WSGI

- Sync only
- Traditional Django
- HTTP only

---

## ASGI

- Async support
- WebSockets
- Long connections

---

## Interview Line

WSGI is sync-only, ASGI supports async and WebSockets.

---

# 95️⃣ Sync vs Async Views

## Sync View

def myview(request):

✔ Blocking

---

## Async View

async def myview(request):

✔ Non-blocking I/O

---

## Use Case

- API calls
- streaming
- sockets

---

## Interview Line

Async views allow non-blocking I/O while sync views block per request.

---

# 96️⃣ Channels kya hote hain?

## Django Channels

Extension for Django  
real-time features add karta hai.

---

## Support

- WebSockets
- chat apps
- live updates

---

## Interview Line

Channels add real-time and WebSocket support to Django.

---

# 97️⃣ WebSocket Django me Kaise?

## Method

Django Channels use karo

---

## Flow

Client ↔ WebSocket ↔ ASGI ↔ Consumer

---

## Use Cases

- Chat
- Notifications
- Live dashboards

---

## Interview Line

WebSockets in Django are implemented using Django Channels over ASGI.



```python

```
