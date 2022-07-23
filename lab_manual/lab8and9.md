1.In the payment_module, open “models.py” and add code for “Invoice” and “InvoiceDetail” models. Note: We use the “user” model from default Django authorization.

from django.contrib.auth.models import User
from product_module.models import Product
...
class Invoice(models.Model):
user = models.ForeignKey(User, on_delete=models.CASCADE)
token = models.UUIDField()
payment_date = models.DateTimeField()
total_amount = models.FloatField()
class InvoiceDetail(models.Model):
invoice = models.ForeignKey(Invoice, on_delete=models.CASCADE)
product = models.ForeignKey(Product, on_delete=models.CASCADE)
quantity = models.IntegerField()
sub_amount = models.FloatField()

2.Run migrations and ensure database table for added model is created properly
python manage.py makemigrations payment_module
python manage.py migrate payment_module

3.In database tool, verify that the added table is created properly.

4.Goto “admin.py” and add the following code
from .models import CartItem
admin.site.register(CartItem)

5.Open product_module then “views.py” add the view for “cart” and “removecart”
@login_required(login_url="/admin/login")
def cart(request):
# get request data
product_id = request.GET.get("id")
quantity = request.GET.get("qty")
if product_id:
# retrieve product data
product = Product.objects.get(id=product_id)
try:
# get cart item and increase quantity
cart_item = CartItem.objects.get(user=request.user,
product=product)
cart_item.quantity += int(quantity)
cart_item.entered_on = datetime.now()
except CartItem.DoesNotExist:
# initialize cart item
cart_item = CartItem(
user=request.user,
product=product,
quantity=int(quantity),
entered_on = datetime.now(),
)
# save to database
cart_item.save()
cart_items = CartItem.objects.filter(user=request.user)
total = 0
for item in cart_items:
total += item.product.price * item.quantity
# return view
context = {
'cart_items': cart_items,
'total': total,
}
return render(request, "cart.html", context)
def removecart(request, id):
try:
# get cart item and increase quantity
product = Product.objects.get(id=id)
cart_item = CartItem.objects.get(user=request.user, product=product)
cart_item.delete()
except CartItem.DoesNotExist:
pass
# redirect to cart
return redirect(cart)
def success_page(request):
message = request.session["message"]
return render(request, "success.html", {"message": message})
def error_page(request):
message = request.session["message"]
return render(request, "error.html", {"message": message})

6.Under “product_module”, make the following adjustments to “urls.py”:
from django.urls import path
from .views import error_page, search, cart, removecart, success_page,
error_page
urlpatterns = [
path('', search),
path('cart/', cart),
path('cart/remove/<int:id>', removecart),
path('success_page/', success_page, name="success_page"),
path('error_page/', error_page, name="error_page"),
]

7.Under “product_module” and “templates” folder, add “success.html”
{% extends "base.html" %}
{% block title %}Success{% endblock %}
{% block header %}
<h2>Payment Successful</h2>
{% endblock %}
{% block content %}
<h3 class="text-success">{{ message }}</h3>
<p>Thank you. Please visit our store again!</p>
<p>
<a class="btn btn-primary" href="/"><i class="fa fa-search" ariahidden="
true"></i> Continue Shopping...</a>
</p>
{% endblock %}

8.Under “product_module” and “templates” folder, add “error.html”
{% extends "base.html" %}
{% block title %}Fail{% endblock %}
{% block header %}
<h2>Payment Failed</h2>
{% endblock %}
{% block content %}
<h3 class="text-danger">{{ message }}</h3>
<p>Please check your token or amount then retry. If issue still exist,
contact your admin!</p>
<p>
<a class="btn btn-info" href="/cart"><i class="fa fa-shopping-cart"
aria-hidden="true"></i> View cart and retry payment</a>
</p>
{% endblock %}

9.Now it’s time to finalize them all. Go to “payment_module”. Under “views.py” add
the following code
from django.shortcuts import render, redirect
from .models import PaymentGateway, Invoice, InvoiceDetail
from product_module.models import CartItem, Product
from datetime import date, datetime
from django.db import transaction
from django.urls import reverse
# Create your views here.
def confirmpayment(request):
if request.method == "POST":
token = request.POST.get("token")
amount = request.POST.get("amount")
# clean up
token = token.strip()
amount = float(amount)
try:
with transaction.atomic():
# open an atomic transaction, i.e. all successful or none
make_payment(token, amount)
maintain_invoice(request, token, amount)
except Exception as e:
request.session["message"] = str(e)
return redirect(reverse('error_page'))
else:
request.session["message"] = f"Payment successfully completed
with NRs. {amount} from your balance!"
return redirect(reverse('success_page'))
def make_payment(token, amount):
try:
payment_gateway = PaymentGateway.objects.get(token=token)
except:
raise Exception(f"Invalid token '{token}'")
# Check if available amount is sufficient for payment
if payment_gateway.balance < amount:
raise Exception("Insufficient balance")
# check for expiry date
if payment_gateway.expiry_date < date.today():
raise Exception("Token has expired")
# deduct amount and save
payment_gateway.balance -= amount
payment_gateway.save()
def maintain_invoice(request, token, amount):
# retrieve cart items
cart_items = CartItem.objects.filter(user=request.user)
# save invoice
invoice = Invoice(
user = request.user,
token = token,
total_amount = amount,
payment_date = datetime.now()
)
invoice.save()
# save invoice detail
for cart_item in cart_items:
invoice_detail = InvoiceDetail(
invoice = invoice,
product = cart_item.product,
quantity = cart_item.quantity,
sub_amount = cart_item.quantity * cart_item.product.price
)
invoice_detail.save()
# adjust product quantity and clear cart
for cart_item in cart_items:
# reduce quantity from Product
product = Product.objects.get(id=cart_item.product.id)
if product.quantity < cart_item.quantity:
raise Exception(f"Insufficient quantity {cart_item.quantity} for
{product.name}")
product.quantity -= cart_item.quantity
product.save()
# clear cart for the user
cart_item.delete()

10.Under “payment_module”, make the following adjustments to “urls.py”:
from django.urls import path
from .views import confirmpayment
urlpatterns = [
path('checkout/', confirmpayment),
]

11.Under main project “ecommerce_yourname”, make the following adjustments to“urls.py”:
    from django.contrib import admin
    from django.urls import path, include
    urlpatterns = [
         path('admin/', admin.site.urls),
         path('', include('product_module.urls')),
         path('payment/', include('payment_module.urls')),
    ]

12.Now the basic implementation of an end-to-end E-Commerce system has been
completed. Run the project and enjoy! Check and verify the following after
checkout has completed:
a.Your amount from token is deducted
b.Cart has been cleared
c.Product quantity has decreased

13.Upload your project to git
    a.Create an account in github (https://github.com/)
    b.Create a new public git repository (e.g. myrepo)
    c.Push your existing code to git
         git remote add origin https://github.com/your/myrepo.git
         git branch -M main
         git push -u origin main
    d.Submit the final lab with the link to your working public git repository
    e.Your final marking will be done on the basis of the repository

14.Next Steps: List out few enhancements/features that a real-time system would
have, and implement those enhancements/features. A list of few enhancements
that can be done are:
a.Marketing campaign, e.g. promo code
b.Payment settings – e.g. Esewa, Paypal integration, etc.
c.Review and rating of product
d.Product tag
e.Dashboard (overview, top selling product, graphs, etc.)
f.Delivery management and tracking code (supply chain)
g.Multiple images
h.Localization (multi language)

15.References:
i. https://www.odoo.com/page/e-commerce-features
ii. https://www.nopcommerce.com/en/features
iii. https://www.opencart.com/index.php?route=cms/feature