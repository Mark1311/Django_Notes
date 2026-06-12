# unique=True vs UniqueConstraint difference?

## unique=True

unique=True ka use hum tab karte hain jab humein kisi Single (ek akeli) Field par unique rule lagana ho. Yeh Django models ke field level par define kiya jata hai."

- Kaam Kaise Karta Hai: Yeh database ko bolta hai ki is column mein koi bhi duplicate value nahi aa sakti.

- Sabse Common Use Cases: email, username, ya phone_number.

Example:
```python
class User(models.Model):
    # Ek akeli email field table mein unique honi chahiye
    email = models.EmailField(unique=True)
```

## UniqueConstraint

UniqueConstraint Django ka ek naya aur advanced feature hai (jo class Meta ke andar define hota hai). Iska use hum tab karte hain jab humein Multiple Fields ko milakar (Composite Unique Key) unique banana ho, ya fir kisi Specific Condition (Conditional Uniqueness) par rule lagana ho.

- Multi-field unique
- Meta level
- Advanced control

Example:
```python
class Student(models.Model):
    class_name = models.CharField(max_length=10)
    roll_number = models.IntegerField()

    class Meta:
        constraints = [
            # Yeh pakka karega ki (Class 10, Roll 1) dubara save na ho sake
            models.UniqueConstraint(fields=['class_name', 'roll_number'], name='unique_class_roll')
        ]
```
| Feature | `unique=True` | `UniqueConstraint` |
| :--- | :--- | :--- |
| **Scope** | Sirf ek single field par lagta hai. | Multiple fields (Composite) aur conditions par lag sakta hai. |
| **Kahan Likha Jata Hai?** | Field ke andar hi attribute ki tarah. | Model ke andar `class Meta` ke `constraints` list mein. |
| **Condition (Filter)** | Isme koi condition nahi laga sakte (Sabhi rows par compulsory apply hoga). | Isme `condition=Q(...)` ka use karke partial/conditional index bana sakte hain. |
| **Django Validation** | Model Form validation ke waqt aasaani se auto-catch ho jata hai. | Django 4.1+ se pehle iska validation automatic Forms mein nahi chalta tha, sirf database level par block hota tha (Ab full support hai). |

---

# AutoField vs BigAutoField

## AutoField (Standard 32-bit Integer)

AutoField ek standard Integer Field hota hai jo automatic increment (1, 2, 3...) hota hai. Yeh parde ke peeche 32-bit Signed Integer ka use karta hai.

- Maximum Capacity (Limit): Iski max limit $2,147,483,647$ (yaani lagbhag 2.1 Billion ya 214 Crore rows) hoti hai.
- Storage Size: Yeh database mein har row ke liye 4 Bytes ki jagah leta hai.
- Kab use hota tha? Django 3.2 se pehle, yeh Django ka default primary key model hua karta ha.  

## BigAutoField (Large 64-bit Integer)

BigAutoField ek bada Integer Field hota hai jo automatic increment hota hai, lekin yeh 64-bit Signed Integer ka use karta hai.

- Maximum Capacity (Limit): Iski max limit $9,223,372,036,854,775,807$ (yaani lagbhag 9 Quintillion rows) hoti hai. Yeh itna bada number hai ki yeh practically kabhi khatam nahi ho sakta.  
- Storage Size: Yeh database mein har row ke liye 8 Bytes ki jagah leta hai.
- Modern Django Default: Django 3.2 aur uske baad ke sabhi versions mein BigAutoField ko hi default primary key banaya gaya hai.


| Feature | `AutoField` | `BigAutoField` |
| :--- | :--- | :--- |
| **Bit Size** | 32-bit Signed Integer | 64-bit Signed Integer |
| **Storage Size** | 4 Bytes per row | 8 Bytes per row |
| **Max ID Limit** | ~2.1 Billion ($2 \times 10^9$) | ~9 Quintillion ($9 \times 10^{18}$) |
| **Django Default** | Django 3.1 aur purane versions mein. | Django 3.2 aur sabhi naye versions mein. |
| **Use Case** | Choti tables ya fixed master data ke liye. | Heavy traffic tables (jaise Logs, Transactions, Analytics). |

# Exists Query kya hoti hai?

exists() Django QuerySet ka ek built-in method hai jiska use yeh check karne ke liye kiya jata hai ki database mein koi record exist (maujood) karta hai ya nahi. Yeh ek Boolean value (True ya False) return karta hai.

Jab hum Django mein .exists() chalate hain, toh Django SQL mein LIMIT 1 laga deta hai. Database jaise hi pehla matching record dhoodh leta hai, wo vahin ruk jata hai aur poori table scan nahi karta.

## Example

from django.db.models import Exists, OuterRef

orders = Order.objects.filter(user=OuterRef("pk"))

users = User.objects.annotate(
    has_orders=Exists(orders)
)

✔ True/False flag milega

---

# select_for_update kya karta hai?

select_for_update ek QuerySet method hai jiska use Database Level Locking ke liye kiya jata hai. Jab hum kisi QuerySet par .select_for_update() lagate hain, toh database un rows ko Lock kar deta hai jab tak hamara poora transaction khatam nahi ho jata. Is beech koi doosra user ya thread un rows ka data UPDATE ya DELETE nahi kar sakta.


## Example
```python
from django.db import transaction
from django.http import HttpResponse
from .models import Wallet

def deduct_money(user_id, amount):
    # 1. Transaction block shuru karna zaroori hai
    with transaction.atomic():
        
        # 2. select_for_update() se yeh row lock ho jayegi
        wallet = Wallet.objects.select_for_update().get(user_id=user_id)
        
        if wallet.balance >= amount:
            wallet.balance -= amount
            wallet.save()
            return "Success"
        else:
            return "Insufficient Funds"
    # Transaction khatam hote hi Lock automatic khul (release) jayega
```

## Use Case

- Payments
- Inventory updates
- Critical counters

---

## Interview Line

select_for_update ka use hum critical sections (jaise banking ledger, e-commerce stock count) mein Race Conditions se bachne ke liye karte hain. Yeh database level par Pessimistic Locking lagata hai, jisse jab tak ek transaction poora nahi hota, tab tak doosra process us data ko modify nahi kar pata.

---

# distinct() kab use karte ho?

distinct() ek QuerySet method hai jiska use database query ke result se Duplicate Rows (Data) ko hatane ke liye kiya jata hai. Yeh SQL ke SELECT DISTINCT clause ka use karta hai. Iska main kaam yeh pakka karna hai ki query humein sirf unique records hi return kare.

## Example

### Case A: Many-to-Many ya Reverse ForeignKey Queries mein (Sabse Common ⚠️)
Maan lijiye aapke paas do models hain: Author aur Book. Ek author ne 5 books likhi hain. Agar aap ek aisi query likhte hain jo un sabhi authors ko nikaale jinki books ke naam mein "Python" aata hai:
```python
# Agar kisi author ne 3 Python books likhi hain, toh uska naam 3 baar repeat hoga
authors = Author.objects.filter(books__title__contains="Python")

# SOLUTION: .distinct() lagane se har author ka naam sirf EK hi baar aayega
authors = Author.objects.filter(books__title__contains="Python").distinct()
```
---

# union() queryset kya hota hai?

union() ek QuerySet method hai jiska use do ya do se zyada QuerySets ke results ko aapas mein jodne (combine karne) ke liye kiya jata hai. Yeh parde ke peeche SQL ke UNION operator ka use karta hai.

### Iska Rule Kya Hai? (The Strict Condition ⚠️)
hum kisi bhi do alag models par union() chala toh sakte hain, lekin un dono QuerySets mein do shartein (rules) honi chahiye:"
1. Dono QuerySets mein Columns ki sankhya (Number of fields) barabar honi chahiye.

2. Dono QuerySets ke columns ka Data Type aur sequence same hona chahiye.
---

## Example
```python
# Step 1: Pehli QuerySet (Sirf name nikaala)
qs1 = User.objects.filter(username__contains="rahul").values('username')

# Step 2: Doosri QuerySet (Isme bhi sirf name nikaala)
qs2 = Group.objects.filter(name__contains="rahul").values('name')

# Step 3: Dono ko database level par jod diya
combined_search = qs1.union(qs2)
```

# Chaining queries kaise kaam karti hain?

Django ORM mein jab hum ek ke baad ek kai saare methods (jaise .filter(), .exclude(), .order_by()) ko ek hi line mein ya lagatar jodte hain, toh use Query Chaining kehte hain. Iska sabse bada fayda yeh hai ki har ek method ek naya QuerySet return karta hai, jisse hum query ko aage aur refine kar sakte hain.

## Example
```python
# Ek hi line mein chain karna
active_premium_users = User.objects.filter(is_active=True).filter(is_premium=True).order_by('-joined_date')
```

## Interview Line

Query Chaining Django ORM ka ek powerful feature hai jo Lazy Evaluation par kaam karta hai. Hum jitne chahe filters chain kar lein, Django database par sirf ek hi final combined SQL query bhejta hai, wo bhi tab jab data ka actual use kiya jaye. Isse hamara code reusable banta hai aur database par load kam hota hai.

---

# Decorators vs Middleware Comparison Table

| Feature | Decorators (फंक्शन-लेवल) | Middleware (ग्लोबल-लेवल) |
| :--- | :--- | :--- |
| **Kaam Karne Ka Dayra (Scope)** | Yeh **Function-level** hote hain. Sirf unhi views par chalte hain jahan aap inhe lagate hain. | Yeh **Application-level (Global)** hote hain. Website ki har ek request aur response isse hokar guzrati hai. |
| **Kahan Define Karte Hain?** | Python ka feature hai. View function ke theek upar **`@`** sign ke sath likha jata hai. | Django Architecture ka part hai. Iska poora path `settings.py` ki `MIDDLEWARE` list mein daala jata hai. |
| **Execution Kaise Hota Hai?** | Jab koi user us specific URL/View par jata hai, sirf tabhi yeh execute hota hai. | Yeh ek proper queue (sequence) mein chalta hai. Request aate waqt upar se neeche, aur response jaate waqt neeche se upar. |
| **Control kiske paas hai?** | **Developer** ke paas. Developer tay karta hai ki kis view ko protect karna hai aur kise nahi. | **System/Framework** ke paas. Yeh automatic parde ke peeche sabhi requests ko monitor karta rehta hai. |
| **Sabse Achha Use-Case** | **Specific Rules:**<br>• `@login_required` (Sirf dashboard ke liye)<br>• `@require_http_methods(["POST"])` (Sirf kisi ek API ke liye) | **Global Rules:**<br>• User Session aur Authentication check karna<br>• Security (CSRF, CORS headers set karna)<br>• Global Logging (Request-Response ka time track karna) |
| **Code Maintenance** | Agar 100 views par lagana ho, toh 100 baar `@` likhna padega. Ek bhi bhool gaye toh security risk (Hacker's chance ❌). | Ek baar `settings.py` mein daal diya, toh poori app secure. Alag se har page par likhne ki jhanjhat nahi (Scalable approach ✅). |


# dispatch() Method kya karta hai?

Class-Based Views (CBVs) mein dispatch() method ek Traffic Cop (Router) ki tarah kaam karta hai. Jab bhi browser se koi HTTP request (jaise GET, POST, PUT, DELETE) hamare view ke paas aati hai, toh sabse pehle dispatch() method hi trigger hota hai. Iska main kaam request ke HTTP Method ko check karna aur use uske sahi handler method (jaise get() ya post()) ke paas bhejna (route karna) hota hai.

---

#  Pagination Kaise Implement Karte Ho?

Pagination ka matlab hota hai bade data (QuerySet) ko chote-chote tukdon (Pages) mein baantkar user ko dikhana. Agar hamare database mein 10,000 products hain, toh hum saare products ek sath ek hi page par nahi dikhate (isse website bohot slow ho jayegi). Hum ek baar mein sirf 10 ya 20 products dikhate hain, aur neeche Next, Previous, ya 1, 2, 3... ke buttons de dete hain.

### Django Mein Ise Kaise Implement Karte Hain?
Django humein pagination handle karne ke liye ek in-built class deta hai: Paginator (jo django.core.paginator se aata hai).

- Pehle database se poora QuerySet nikaalo.
- Paginator class ko wo QuerySet aur har page par kitna data dikhana hai (item count) pass karo.
- URL se current page number (jaise ?page=2) uthaao.
- Us page ka specific data nikaal kar frontend template ko bhej do.

#### Pure Django (Function-Based View) Code Example
```python
from django.shortcuts import render
from django.core.paginator import Paginator
from .models import Product

def product_list_view(request):
    # 1. Saare products ka queryset nikala (Lazy Evaluation ki wajah se load nahi hoga)
    all_products = Product.objects.all().order_by('id')
    
    # 2. Paginator setup kiya (Har page par sirf 10 products dikhane hain)
    paginator = Paginator(all_products, 10) 
    
    # 3. URL se current page number uthaya (E.g., /products/?page=2)
    # Agar page parameter nahi hai, toh default 1st page dikhao
    page_number = request.GET.get('page', 1)
    
    # 4. Us specific page ka data (object) nikaala
    page_obj = paginator.get_page(page_number)
    
    # 5. Frontend template ko bhej diya
    return render(request, 'products.html', {'page_obj': page_obj})
```
```html
{% for product in page_obj %}
    <p>{{ product.name }}</p>
{% endfor %}

<div class="pagination">
    {% if page_obj.has_previous %}
        <a href="?page=1">&laquo; First</a>
        <a href="?page={{ page_obj.previous_page_number }}">Previous</a>
    {% endif %}

    <span>Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}</span>

    {% if page_obj.has_next %}
        <a href="?page={{ page_obj.next_page_number }}">Next</a>
        <a href="?page={{ page_obj.paginator.num_pages }}">Last &raquo;</a>
    {% endif %}
</div>
```

## Interview Line

Django ka Paginator parde ke peeche SQL ke LIMIT aur OFFSET clauses ka use karta hai. Is wajah se database se ek baar mein sirf utna hi data uthta hai jitna us page par dikhana ho, jisse server ki RAM aur performance dono safe rehti hain. Haalanki, bohot bade datasets (jaise karodon rows) ke liye OFFSET slow ho jata hai, tab hum standard pagination ki jagah Cursor Pagination ka use karte hain.


# clean() vs clean_fieldname() Difference

## clean_fieldname() — Single Field Validation

clean_<fieldname>() ka use hum tab karte hain jab humein kisi ek single input field par custom validation lagani ho. Yahan <fieldname> ki jagah hamari actual form field ka naam aata hai, jaise clean_username() ya clean_email().

- Kaam Kaise Karta Hai: Yeh automatic trigger hota hai jab Django us specific field ka data check kar raha hota hai.

- Sabse Zaroori Baat ⚠️: Is method ke aakhiri mein validate kiya hua data return karna compulsory (zaroori) hota hai, nahi toh wo field khali (None) ho jayegi.

Example:
```python
from django import forms
from django.core.exceptions import ValidationError

class RegisterForm(forms.Form):
    username = forms.CharField(max_length=100)

    # Sirf username field ko validate karne ke liye
    def clean_username(self):
        data = self.cleaned_data['username']
        if "admin" in data.lower():
            raise ValidationError("Aap 'admin' naam ka username nahi rakh sakte!")
        return data  # Data return karna zaroori hai!
```

## clean() — Multi-Field / Cross-Field Validation

clean() method ka use tab kiya jata hai jab humein multiple fields ko aaoas mein compare karna ho (Cross-Field Validation), ya poore form ke level par koi global check lagana ho.

- Kaam Kaise Karta Hai: Yeh tab chalta hai jab saari individual clean_<fieldname>() queries chal chuki hoti hain aur Django ke paas saari fields ka processed data aa jata hai.

- Sabse Zaroori Baat ⚠️: Isme hum pooray form ka saaf kiya hua data yaani self.cleaned_data return karte hain.

Example:-
```python
class RegisterForm(forms.Form):
    password = forms.CharField(widget=forms.PasswordInput)
    confirm_password = forms.CharField(widget=forms.PasswordInput)

    # Poore form ke level par validation (Multiple fields compare karne ke liye)
    def clean(self):
        # Parent class ka clean method call karke base cleaned_data uthaya
        cleaned_data = super().clean()
        
        password = cleaned_data.get("password")
        confirm_password = cleaned_data.get("confirm_password")

        # Dono fields ko aapas mein compare kiya
        if password and confirm_password and password != confirm_password:
            raise ValidationError("Dono passwords match nahi ho rahe hain!")
            
        return cleaned_data  # Poora dictionary return karna zaroori hai!
```
| Feature | `clean_<fieldname>()` | `clean()` |
| :--- | :--- | :--- |
| **Scope** | **Single Field Level** (Sirf ek field par kaam karta hai). | **Form Level / Multi-Field** (Poore form par kaam karta hai). |
| **Execution Order** | Yeh **pehli stage** mein chalta hai (Field by field). | Yeh **aakhiri stage** mein chalta hai (Jab saari fields individual clean ho chuki hon). |
| **Kya Return Karta Hai?** | Sirf us **specific field ki value** return karta hai. | Poori **`cleaned_data` ki dictionary** return karta hai. |
| **Error Kahan Dikhta Hai?** | Error specific field ke theek neeche browser par dikhta hai. | Error poore form ke top par (Global Error) dikhta hai. |
| **Best Used For** | Kisi ek field ka criteria check karna (E.g., Email ka unique hona). | Do ya do se zyada fields ko compare karna (E.g., Password aur Confirm Password). |

---

# Formset kya hota hai?

simple bhasha mein kahein toh Formset ek hi Django Form ke multiple instances (ek se zyada copies) ka ek collection ya group hota hai. Agar aapke paas ek ProductForm hai jisse ek baar mein ek product save hota hai, toh Formset ka use karke aap ek hi page par user ko 5 ya 10 products ka form ek sath dikha sakte hain aur unka data ek hi click mein submit kar sakte hain.

## Use Cse:-

### 1. Formset Kya Hota Hai? (The Definition):-
simple bhasha mein kahein toh Formset ek hi Django Form ke multiple instances (ek se zyada copies) ka ek collection ya group hota hai. Agar aapke paas ek ProductForm hai jisse ek baar mein ek product save hota hai, toh Formset ka use karke aap ek hi page par user ko 5 ya 10 products ka form ek sath dikha sakte hain aur unka data ek hi click mein submit kar sakte hain."

### 2. Iski Zaroorat Kab Padti Hai? (The Real-World Scenario)
Maan lijiye aap ek billing software bana rahe hain. Jab ek customer checkout karta hai, toh ek baar mein 5 alag-alag items khareed sakta hai. Wahan hum ek hi page par item ka naam, quantity, aur price ki multiple rows dikhate hain.

## Example
```python
from django.shortcuts import render
from django.forms import formset_factory
from django import forms

# 1. Ek normal simple form banaya
class ArticleForm(forms.Form):
    title = forms.CharField(max_length=100)
    pub_date = forms.DateField()

def manage_articles(request):
    # 2. Formset banaya jo ArticleForm ki 3 khali copies dikhayega
    ArticleFormSet = formset_factory(ArticleForm, extra=3)
    
    if request.method == 'POST':
        formset = ArticleFormSet(request.POST)
        if formset.is_valid():
            # 3. Saare forms ka data ek sath loop karke bacha sakte hain
            for form in formset:
                print(form.cleaned_data.get('title'))
            return HttpResponse("Sabhi forms successfully submit ho gaye!")
    else:
        formset = ArticleFormSet()
        
    return render(request, 'manage_articles.html', {'formset': formset})
```

# FileField vs ImageField Difference

## FileField
FileField ek general-purpose field hai jiska use kisi bhi type ki file ko upload karne ke liye kiya jata hai. Isme aap PDF, ZIP, MP3, MP4, CSV, DOCX ya Images—kuch bhi upload kar sakte hain.

- Any file type allow
- No image validation

Example:
```python
file = models.FileField(upload_to="docs/")
```

## ImageField
ImageField ek specialized field hai jo FileField ka hi ek subclass (child) hai. Iska use sirf aur sirf Images (photos) ko upload karne ke liye kiya jata hai.

- Sirf images allow
- Pillow library required
- Image validation built-in

Example:
```python
image = models.ImageField(upload_to="images/")
```

# write_only vs read_only Fields

## read_only=True Fields (Sirf Output Ke Liye 📤)
read_only=True fields ka use tab kiya jata hai jab humein koi data sirf API Response (Output) mein dikhana ho, lekin hum nahi chahte ki user use Request (Input) ke waqt badal sake ya bhej sake.


- Kaam Kaise Karta Hai: Yeh fields API response mein serializer data ke sath bahar jayengi. Agar koi user POST ya PUT request ke waqt is field mein koi naye value bhejega, toh DRF use chupchaap ignore kar dega (database mein save nahi hone dega).

- Sabse Common Use Cases: id, created_at, updated_at, is_staff, ya total score/calculation fields.

Example:
```python
class ProfileSerializer(serializers.ModelSerializer):
    # User is_verified ko khud se True nahi kar sakta, ye sirf server dikhayega
    is_verified = serializers.BooleanField(read_only=True)
```

## write_only=True Fields (Sirf Input Ke Liye 📥)
write_only=True fields ka use tab kiya jata hai jab humein koi data sirf API Request (Input) mein lena ho, lekin security ya privacy ki wajah se hum use API Response (Output) mein wapas nahi dikhana chahte.

- Kaam Kaise Karta Hai: Yeh fields user se POST, PUT ya PATCH request ke waqt data accept karegi aur validation mein kaam aayegi. Lekin jaise hi API wapas response (JSON) degi, yeh field usme se गायब (hide) ho jayegi.

- Sabse Common Use Cases: password, confirm_password, ya secret API keys/tokens.

Example:
```python
class RegisterSerializer(serializers.ModelSerializer):
    # Password validation aur save ke liye chahiye, par response JSON mein nahi dikhna chahiye
    password = serializers.CharField(write_only=True)
```

| Feature | `read_only=True` | `write_only=True` |
| :--- | :--- | :--- |
| **API Flow Direction** | **Output Only** (Server $\rightarrow$ Client) | **Input Only** (Client $\rightarrow$ Server) |
| **Request (POST/PUT)** | Django is field ke data ko ignore kar deta hai. | Django is field ke data ko accept aur validate karta hai. |
| **Response (JSON)** | Yeh field response JSON mein saaf dikhayi deti hai. | Yeh field response JSON se poori tarah hide ho jaati hai. |
| **Security Angle** | User ko database ke internal auto-generated data ko modify karne se rokta hai. | Sensitive data (jaise passwords) ko response mein leak hone se bachata hai. |
| **Best Example** | `id`, `created_at`, `status` | `password`, `old_password` |


# Throttling kya hoti hai?
Throttling ka matlab hota hai kisi user ya IP address par API requests ki ek limit (border) tay karna. Yeh control karta hai ki koi user ek minute, ek ghante, ya ek din mein hamari API ko kitni baar hit kar sakta hai. Agar user us limit ko cross karta hai, toh server use HTTP 429 Too Many Requests ka error de deta hai.

Yeh bilkul Permissions jaisa hi hota hai, farq bas itna hai ki Permissions yeh check karta hai ki "Kya aap is API ko access kar sakte hain?", aur Throttling yeh check karta hai ki "Aap is API ko kitni baar access kar sakte hain?

## Iski Zaroorat Kab Aur Kyun Padti Hai? (The Purpose)

- DDOS Attacks aur Brute Force se bachne ke liye: Koi hacker script chala kar aapke Login API par 1 second mein 10,000 passwords try kar raha ho (Brute force). Throttling use 5 requests ke baad hi block kar degi.

- Server Cost aur Performance bachane ke liye: Agar koi user lagatar aapki heavy search API ko hit karega, toh database crash ho sakta hai.

- Business Model / Paid APIs (Rate Limiting): Jaise OpenAI ya Weather APIs karti hain—Free users ko kam requests milti hain (E.g., 60 per minute) aur Paid users ko zyada.


# Token vs JWT Difference

## Token Auth
Django ka default Token Authentication ek Stateful tareeqa hai. Isme jab user login karta hai, toh Django server ek random string (Token) generate karta hai aur use Database (authtoken_token table) mein save kar leta hai

- Request Flow: Jab bhi frontend koi doosri request bhejta hai, toh use header mein yeh token bhejni padti hai (Authorization: Token <key>).

- The Catch ⚠️: Django har ek aane wali request par database hit karega aur check karega ki yeh token database mein exist karti hai ya nahi aur kis user ki hai.

---

## JWT (JSON Web Token) Authentication (The Stateless Way)
JWT ek Stateless aur Cryptographically Secure tareeqa hai. Isme login karne par server jo token generate karta hai, use database mein save nahi kiya jata. Token ke andar hi user ki saari basic details (jaise user_id, email) ek encrypted string ke roop mein maujood hoti hain.

- Request Flow: Frontend jab request bhejta hai (Authorization: Bearer <token>), toh Django server bina database ko chhue, sirf apne Secret Key ki madad se us token ko cryptographically verify (decode) kar leta hai aur jaan jata hai ki user kaun hai.

#### JWT ke 3 Parts hote hain (Semicolon . se separated):

- Header: Batata hai ki kaunsa algorithm use hua hai (E.g., HS256).
- Payload: Isme actual data hota hai (User ID, Name, Expiry time) jise Claims kehte hain.
- Signature: Yeh sabse secure part hai jo server ki secret key se banta hai taaki koi token ke sath chhed-chhad (tamper) na kar sake.

## Difference

| Feature | Simple Token (DRF Default) | JWT (Simple JWT) |
| :--- | :--- | :--- |
| **Architecture** | **Stateful**: Database par depend hota hai. | **Stateless**: Database se koi matlab nahi hota. |
| **Database Hits** | **High**: Har ek API request par database query chalti hai token verify karne ke liye. | **Zero**: Server bina database check kiye token ko decode/verify kar leta hai. |
| **Token Size** | Chota hota hai (Sirf ek random string, jaise 40 chars). | Bada hota hai (Kyunki isme user ka data aur signature encrypted hota hai). |
| **Scalability** | Kam scalable hai. Agar 10 lakh users ek sath request karenge toh DB crash ho sakta hai. | **Highly Scalable**: Multiple microservices ya servers par bina kisi central DB ke chal jata hai. |
| **Token Revocation (Logout)** | **Aasan hai**: Database se token row delete kar do, user automatic logout ho jayega. | **Mushkil hai**: Kyunki token server se nahi handle ho rahi, iske liye Blacklisting/Refresh token use karna padta hai. |
| **Expiry Control** | By default expire nahi hoti (Jab tak manually delete na kiya jaye). | In-built short expiry hoti hai (Access Token 5 mins, Refresh Token 1 day). |


# Redis kyu use karte hain?

Redis ka full form hai REmote DIctionary Server. Yeh ek open-source, In-Memory, Key-Value Data Store hai. Iska matlab yeh hai ki yeh hamare primary databases (jaise PostgreSQL ya MySQL) ki tarah hard disk par data save nahi karta, balki saara data RAM (Memory) mein rakhta hai.

Kyunki RAM ki read/write speed hard disk se hazaaron guna zyada hoti hai, isliye Redis ultra-fast kaam karta hai (micro-seconds mein data return karta hai). Isme data Python ki dictionary ki tarah Key: Value pair mein store hota hai.

> Django ke paas apna database hai, toh alag se Redis lagane ki kya zaroorat hai?

## A. Database Caching (Performance Badhane Ke Liye 🚀):-
Redis ka kamaal: Hum pehli baar database se data nikaal kar use Redis (Cache) mein save kar dete hain. Agli baar jab koi user aayega, Django database ke paas nahi jayega, balki Redis se 1 millisecond mein data utha kar user ko de dega.

## B. Celery Ke Sath As a Message Broker (Background Tasks ⏳):- 
Redis ka kamaal: Hum Celery ka use karte hain background tasks ke liye, aur Redis wahan Broker banta hai. Django user ko turant "Success" bol deta hai aur email bhejne ka task Redis queue mein daal deta hai. Celery parde ke peeche se use utha kar chalati rehti hai.

## C. Django Channels (Real-Time Features 💬):-
Redis ka kamaal: Redis yahan Channel Layer ka kaam karta hai. Yeh alag-alag users aur server ke sockets ke beech mein messages ko bohot fast transfer (broadcast) karne mein madad karta hai.

## Django Me Use

- Cache backend
- Celery broker
- Sessions
- Rate limiting

| Feature | Primary DB (PostgreSQL / MySQL) | Redis (In-Memory Store) |
| :--- | :--- | :--- |
| **Storage Location** | **Hard Disk / SSD** (Data permanent rehta hai). | **RAM / Memory** (Server restart par data ud sakta hai). |
| **Speed** | Medium (Disk I/O ki wajah se thoda time lagta hai). | **Ultra-Fast** (RAM par hone ke kaaran fast hota hai). |
| **Data Structure** | Tables (Rows aur Columns). | Key-Value Pairs (Strings, Lists, Hashes). |
| **Primary Role** | Main Application Data ko safely save rakhna. | **Caching, Queues (Celery), aur Real-time data routing**. |

---

#  Query Optimization Steps

- select_related()
- prefetch_related()
- only()/defer()
- indexes add
- pagination
- avoid N+1
- values() use
- annotate instead loops


# Celery kya hota hai?

Celery ek Distributed Task Queue hai jiska use Python/Django mein Asynchronous (Background) Tasks aur Scheduled Tasks (Cron Jobs) ko chalane ke liye kiya jata hai.

Simple bhasha mein kahein toh Django synchronous kaam karta hai—yaani wo ek baar mein ek hi kaam karega. Agar koi aisa kaam hai jo bohot zyada time leta hai, toh Django user ki screen ko block kar deta hai aur user ko wait karna padta hai. Celery us heavy kaam ko Django se lekar parde ke peeche (background mein) alag se chalata hai, jisse user ka experience kharab nahi hota.

## Use

- Emails
- Reports
- Image processing
- Heavy jobs

---

# Django Production Settings kya change karte ho?

## Important Changes

- DEBUG = False
- Proper DB config
- Static/media config
- Cache enable
- Secure cookies
- Logging enable
- Allowed hosts set

---

# ALLOWED_HOSTS kya hai?

ALLOWED_HOSTS Django ki settings.py file mein ek Security Configuration Setting hai. Yeh ek Python list hoti hai jisme hum un Domain Names ya IP Addresses ke naam likhte hain jinpar hamari Django website chal sakti hai.

Jab aapki website internet par live hoti hai, toh Django har ek aane wali request ke HTTP Host Header ko check karta hai. Agar aane wali request ka domain is list mein maujood nahi hoga, toh Django use block kar dega aur SuspiciousOperation / HTTP 400 Bad Request ka error throw karega.
---

## Example
```python
# settings.py

# 1. Development Mode (Khali chhod sakte hain)
DEBUG = True
ALLOWED_HOSTS = []

# 2. Production Mode (Domains aur IPs likhna zaroori hai)
DEBUG = False
ALLOWED_HOSTS = [
    'www.mywebsite.com',   # Aapka Main Domain
    'mywebsite.com',       # Without www
    '13.233.5.45',         # Aapke AWS EC2 ya VPS ka Public IP
    '.mywebsite.com',      # Subdomains ko allow karne ke liye (E.g., blog.mywebsite.com)
]
```

# Nginx role kya hai?
Nginx ek high-performance, open-source Web Server aur Reverse Proxy hai. Production environment mein yeh hamari Django application ke aage ek Security Guard (Front Gate) ki tarah khada rehta hai. Internet se aane wali har ek request sabse pehle Nginx ke paas aati hai, aur Nginx use sahi jagah forward karta hai."

## Nginx kya karta hai?

### A. Reverse Proxy (Request Routing)
Nginx direct internet se requests leta hai aur use parde ke peeche chal rahe Django server (Gunicorn) ko pass kar deta hai. Isse hamara Django application direct internet par expose nahi hota, jo security ke liye bohot zaroori hai.

### B. Static aur Media Files Handling (Ultra-Fast Serving 🚀)
Django dynamic content (database queries, logic) banane ke liye bana hai, static files (CSS, JS, Images) serve karne ke liye nahi.

### C. Load Balancing (Traffic Management)
Agar aapki website par lakhon users aa gaye aur aapne Django ke 3 alag-alag servers chala rakhe hain, toh Nginx ek Traffic Cop ki tarah kaam karega. Wo aane wali requests ko barabar-barabar teeno Django servers par baant (distribute) dega taaki koi ek server crash na ho.


# Agar Site Slow Ho Jaye To Kya Check Karoge?

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

# WSGI vs ASGI Difference

- WSGI (Web Server Gateway Interface): Yeh Python ka purana aur standard tareeqa hai. Yeh sirf Synchronous (Ek ke baad ek) applications ko support karta hai. Iska matlab hai ki jab ek request aayegi, toh jab tak uska kaam poora nahi hota, server doosri request par aage nahi badhega (Block ho jayega).

- ASGI (Asynchronous Server Gateway Interface): Yeh WSGI ka hi ek advanced, modern successor (agla roop) hai. Yeh Asynchronous (Ek sath kai kaam) ko support karta hai. Isme server bina pehli request ke khatam hone ka wait kiye, doosri request ko process karna shuru kar deta hai (Non-blocking).

### How this work:-

- WSGI ek Single-Lane Road ki tarah hai: Agar aage ek badi truck (heavy database query ya file upload) dheere chal rahi hai, toh uske peeche ki saari gaadiyan (requests) phas jayengi aur unhe wait karna padega.

- ASGI ek Multi-Lane Highway ki tarah hai: Agar ek request heavy kaam kar rahi hai, toh ASGI use side mein daal deta hai aur baaki lanes se doosri requests ko fast nikalne deta hai. Jab pehli request ka kaam poora hota hai, wo wapas line mein aa jati hai.

| Feature | WSGI (Purana Standard) | ASGI (Naya Standard) |
| :--- | :--- | :--- |
| **Full Form** | Web Server Gateway Interface | Asynchronous Server Gateway Interface |
| **Execution Mode** | **Synchronous Only** (Blocking nature). | **Asynchronous & Synchronous** Both (Non-blocking). |
| **Protocols Supported** | Sirf standard **HTTP** aur HTTPS handle karta hai. | **HTTP, WebSockets, aur HTTP/2** sabhi ko handle karta hai. |
| **Real-time Features** | Chat apps, Live Notifications ke liye bekar hai (Long polling karni padti hai). | **Perfect hai** real-time apps, chat systems, aur live streaming ke liye. |
| **Popular Servers** | **Gunicorn**, uWSGI, Mod_WSGI | **Uvicorn**, Daphne, Hypercorn |
| **Django Integration** | Django 1.0 se hi default hai (`wsgi.py`). | Django 3.0+ se in-built support aaya hai (`asgi.py`). |


# Sync vs Async Views

## Sync View
Django mein by default saare views Synchronous (Sync) hote hain. Iska matlab yeh hai ki jab koi request aati hai, toh Django ka thread us request par block ho jata hai jab tak ki saara kaam (jaise database query chalna ya koi API hit hona) khatam na ho jaye."

The Problem: Agar aapke view mein koi aisa kaam hai jo 5 seconds leta hai (jaise ek external weather API ko call karna), toh wo thread un 5 seconds ke liye poori tarah busy ho jayega. Agar aapke paas limited threads hain, toh baaki users ki requests waiting queue mein phas jayengi.

## Async View
Django 3.1 ke baad se hum views ko Asynchronous (Async) bana sakte hain Python ke async def keyword ka use karke. Async views ka sabse bada fayda yeh hai ki yeh Non-blocking hote hain aur Python ke Event Loop par chalte hain."

How it works: Agar ek async view kisi third-party API ka wait kar raha hai, toh wo thread ko block nahi karega. Wo control wapas event loop ko de dega taaki server tab tak doosre users ki requests ko handle kar sake. Jaise ہی API ka response aayega, event loop is view ko wapas utha kar bacha hua kaam poora kar dega.

## Use Case
- API calls
- streaming
- sockets

Example:-
```python
import httpx
import asyncio
from django.http import HttpResponse

# --- 1. SYNC VIEW (Blocking) ---
def sync_weather_view(request):
    # Yeh line 3 seconds ke liye pure thread ko block kar degi
    response = httpx.get('https://api.weather.com/v1/data') 
    return HttpResponse(f"Weather Data: {response.text}")


# --- 2. ASYNC VIEW (Non-Blocking) ---
async def async_weather_view(request):
    # Yahan 'async with' aur 'await' use hua hai
    # Jab tak response aayega, server doosre kaam kar sakta hai
    async with httpx.AsyncClient() as client:
        response = await client.get('https://api.weather.com/v1/data')
        
    return HttpResponse(f"Weather Data: {response.text}")
```

# Channels kya hote hain?

Django Channels ek aisa extension (plugin) hai jo Django ki capability ko standard HTTP se aage badha kar WebSockets, MQTT, aur Chat Protocols ko support karne ke laayak banata hai. Yeh Django ko synchronous (sync) se asynchronous (async) bana deta hai, jisse hum real-time applications asani se bana sakte hain."

Normal Django sirf HTTP requests handle karta hai jahan Client request bhejta hai aur Server response deta hai (Request-Response cycle). Lekin Django Channels ki madad se Server bhi khud se Client ko bina maange data bhej sakta hai (Persistent Connection).

### Iski Zaroorat Kab Aur Kyun Padti Hai?

- Real-Time Chat Applications: Jaise WhatsApp ya Slack, jahan jaise hi koi message aaye, samne wale ki screen par turant bina page refresh kiye dikh jaye.

- Live Notification Systems: Facebook ya Instagram ki tarah—kisi ne aapki photo/post like ki, aur aapko upar turant ek alert ya pop-up mil gaya.

- Live Tracking / Dashboards: Uber ya Zomato jaisa live map tracking, ya fir Stock Market (Crypto) ka live price tick-by-tick badalna.

# WebSocket Django me Kaise?

Production par hum project ko run karne ke liye ek ASGI server jaise Uvicorn ya Daphne ka use karte hain. Hum command chalate hain:
uvicorn myproject.asgi:application --host 0.0.0.0 --port 8000
Iske aage hum Nginx lagate hain, jisme hum proxy_set_header Upgrade $http_upgrade; aur proxy_set_header Connection "Upgrade"; configure karte hain taaki Nginx normal HTTP connection ko WebSocket connection mein 'Upgrade' kar sake.

Django mein WebSocket implement karne ke liye hum Django Channels library ka use karte hain. Isme hum settings mein ASGI_APPLICATION set karte hain, urls.py ki jagah routing.py banate hain, aur logic handle karne ke liye AsyncWebsocketConsumer ka use karte hain. Parde ke peeche multi-user communication ke liye Redis Channel Layer ka use kiya jata hai aur production par ise Uvicorn server ke throw run karte hain.

