Preparing Cart
1.In the module, open “models.py” and add code for “cart_item” model. Note: We use
the “user” model from default Django authorization.

from django.contrib.auth.models import User

class CartItem(models.Model):
    user = models.ForeignKey(User , on_delete=models.CASCADE)
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    quantity = models.IntegerField()
    entered_on = models.DateTimeField()

2.Run migrations and ensure database table for added model is created properly
A.python manage.py makemigrations product_module
B.python manage.py migrate product_module

3.In database tool, verify that the added table is created properly.

4.Goto “admin.py” and add the following code
from .models import CartItem
admin.site.register(CartItem)

5.In “views.py” add the view for “cart”
from django.shortcuts import render, redirect
from django.contrib.auth.decorators import login_required
from datetime import datetime
from .models import Product, CartItem
...
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
# retrieve the cart items for the user from db
cart_items = CartItem.objects.filter(user=request.user)
# calculate total
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
# get cart item and remove it
product = Product.objects.get(id=id)
cart_item = CartItem.objects.get(user=request.user, product=product)
cart_item.delete()
except CartItem.DoesNotExist:
pass
# redirect to cart
return redirect(cart)

6.In “templates”, create a file “cart.html” and enter the following:
{% extends "base.html" %}
{% block title %} Cart {% endblock %}
{% block header %} Your Cart {% endblock %}
{% block content %}
{% if cart_items %}
<div class="row">
<h4 class="col-sm-12 col-md-12 col-lg-12">
<span class="text-muted"><i class="fa fa-shopping-cart" ariahidden="true"></i> Your cart</span>
<span class="badge badge-secondary badgepill">{{cart_items|length}}</span>
</h4>
</div>
<table class="table table-striped">
<thead>
<tr>
<th>Image</th>
<th>Name</th>
<th>Price</th>
<th>Quantity</th>
<th>Action</th>
</tr>
</thead>
<tbody>
{% for item in cart_items %}
<tr>
<td><img height="50" width="50" src="{{item.product.image_url }}" /></td>
<td>{{ item.product.name }}</td>
<td><h5>US${{ item.product.price }}</h5></td>
<td><h5>{{ item.quantity }}</h5></td>
<td>
<form method="post" action="/cart/remove/{{item.product.id}}">
{% csrf_token %}
<button type="submit" class="btn btn-danger" type="submit"><i class="fa fa-times" aria-hidden="true"></i> Remove</button>
</form>
</td>
</tr>
{% endfor %}
<tr>
<td></td>
<td style="text-align: right;"><h3>Total Price:</h3></td>
<td><h3>US${{ total }}</h3></td>
<td>
<form method="post" action="/payment/checkout/">
{% csrf_token %}
<div class="input-group">
<input type="text" class="form-control" name="token" placeholder="Token" />
<input type="hidden" name="amount" value="{{ total
}}" />
<button type="submit" class="btn btn-primary" type="submit"><i class="fa fa-shopping-cart" aria-hidden="true"></i>Checkout Payment</button>
</div>
</form>
</td>
<td>
<button onclick="location.href='/';" class="btn btn -info">
<i class="fa fa-search" aria-hidden="true"></i> Back to shop!</button>
</td>
</tr>
</tbody>
</table>
{% else %}
<h2>No product added to cart.</h2>
<button onclick="location.href='/';" class="btn btn-info"><i class="fa fa-search" aria-hidden="true"></i> Back to shop!</button>
{% endif %}
{% endblock %}

7.In “urls.py”, add the following:
from django.urls import path
from .views import search, cart, removecart
urlpatterns = [
path('', search),
path('cart/', cart),
path('cart/remove/<int:id>', removecart),
]

8.Run the project and navigate to admin to check the result.
python manage.py runserver

9.Go to browser and navigate your project url.
A.In browser, open 'http://127.0.0.1:8000/admin' for admin interface
B.In browser, open 'http://127.0.0.1:8000 for user interfrace (search)
C.Perform ‘Add to Cart’ operation for various products
D.Perform cart item removal operation