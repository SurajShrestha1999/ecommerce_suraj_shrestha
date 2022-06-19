Lab 4

Search Products (User)

A.Procedure:
1.Before implementing search UI, first prepare template for Django. Create a
directory “templates” and add a base html template file “base.html”.
<!DOCTYPE html>
<html lang="en">
<head>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css"/>
<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/fontawesome/4.7.0/css/font-awesome.min.css"/>
<title>{% block title %} Electronics Commerce {% endblock %}</title>
<style type="text/css">
    .min-h-100 { min-height: 100%; }
</style>
</head>
<body>
<div class="row">
<div id="header" class="bg-info col-sm-12 col-md-12 col-lg-12">
<div class="row">
<div class="col-sm-4 col-md-4 col-lg-4">
<img src="https://img.favpng.com/23/7/24/logo-e-commercedigital-
marketing-brand-trade-png-favpng-xTcxcPuHCYQBUh9P8q30ETQji.jpg"
alt="E-commerce Logo" style="height:50px; width:auto;" />
</div>
<div class="col-sm-7 col-md-7 col-lg-7">
<h2>
{% block header %} Electronic Commerce {% endblock %}
</h2>
</div>
<div class="col-sm-1 col-md-1 col-lg-1">
<a class="btn btn-success btn-sm ml-3" href="#cart-model"
data-toggle="modal">
<span>Cart</span>
<span class="badge badge-light">
<label id="cart_qty">0</label>
</span>
</a>
</div>
</div>
</div>
</div>
<div class="row">
<div id="sidebar" class="min-h-100 min-h-800 bg-light border col-sm-3 col-md-3 col-lg-3">
{% block sidebar %}
<ul>
<li><a href="/admin/"><i class="fa fa-user" ariahidden="true"></i> Admin</a></li>
<li><a href="/"><i class="fa fa-search" aria-hidden="true"></i>Product</a></li>
<li><a href="/cart/"><i class="fa fa-shopping-cart" ariahidden="true"></i> Cart</a></li>
</ul>
{% endblock %}
</div>
<div id="content" class="min-h-100 bg-light col-sm-9 col-md-9 col-lg-9">
{% block content %}{% endblock %}
</div>
</div>
</body>
</html>

2.To ensure that the “templates/base.html” is available globally, adjust the TEMPLATES/DIRS setting. Go to “settings.py” and make the following adjustment.

TEMPLATES = [
{
'BACKEND': 'django.template.backends.django.DjangoTemplates',
'DIRS': [BASE_DIR / 'templates'],
}
]

3.Inside “product_module”, create a directory “templates”. Note that this “templates” folder is different from Step #1. Create a html “index.html”
{% extends "base.html" %}
{% block title %} Search {% endblock %}
{% block header %} Search Product {% endblock %}
{% block content %}
<!--Navbar-->
<nav class="navbar navbar-expand-lg">
<div>
<!-- Links -->
<ul class="navbar-nav mr-auto">
<li class="nav-item active">
<a class="nav-link text-dark" href="/ ">All</a>
</li>
{% for category in categories %}
<li class="nav-item">
<a class="nav-link text-dark"
href="/?category={{category.id}}">{{category.name}}</a>
</li>
{% endfor %}
{% for brand in brands %}
<li class="nav-item">
Electronic Commerce (Programming)
12
<a class="nav-link text-dark"
href="/?brand={{brand.id}}">{{brand.name}}</a>
</li>
{% endfor %}
</ul>
<!-- Links -->
</div>
<div>
<form class="form-inline" method="POST">
{% csrf_token %}
<div>
<input name="query" class="form-control" type="text"
placeholder="Search/enter price-range" aria-label="Search"
value="{{search_query}}">
</div>
</form>
</div>
</nav>
<!--/.Navbar-->
{% for product in products %}
<div class="row border bg-light">
<div class="col-md-4">
<div class="text-center">
<img src="{{ product.image_url }}" style="height:200px;
width:auto;" alt="{{ product.name }}">
</div>
</div>
<div class="col-md-3">
<span class="btn btn-danger disabled">{{ product.brand.name
}}</span>
<span class="btn btn-info disabled">{{ product.category.name
}}</span>
<h3>{{ product.name }}</h3>
<h4 class="bold text-secondary">
<strong>NRs. {{ product.price }}</strong>
</h4>
<form class="d-flex" action="/cart" method="GET">
<!-- Default input -->
<input type="hidden" name="id" value="{{product.id}}" />
<input type="number" name="qty" value="1" arialabel="
Search" class="form-control" style="width: 100px">
<button id="btn-add-to-cart" class="btn btn-primary btn-md"
type="submit" data-toggle="modal" data-target="#cart-model"><i class="fa fashopping-
cart" aria-hidden="true"></i> Add to cart</button>
</form>
</div>
<div class="col-md-5">
<table class="table table-sm">
<tr>
<td>Available Quantity</td>
<td>{{product.quantity}}</td>
</tr>
<tr>
<td>Color Code</td>
<td><div style="height: 25px; width: 25px; backgroundcolor:
{{ product.color_code }};"></div></td>
</tr>
<tr>
<td>Brand</td>
<td>{{ product.brand.name }}</td>
</tr>
<tr>
<td>Category</td>
<td>{{ product.category.name }}</td>
</tr>
<tr>
<td>Registered On</td>
<td>{{ product.registered_on }}</td>
</tr>
<tr>
<td>Is Active</td>
<td>
{% if product.is_active %}
<input type="checkbox" checked />
{% else %}
<input type="checkbox" />
{% endif %}
</td>
</tr>
</table>
</div>
</div>
{% endfor %}
{% endblock %}

4.From the project “product_module” open “views.py” and add the code as below for search operation (GET and POST).
from django.db.models import Q
from .models import Product, Brand, Category
...
def index(request):
    if request.method == "GET": 
        category_id = request.GET.get("category")
        brand_id = request.GET.get("brand")
        if category_id:
            filter_query = Q(category__id=category_id)
            products = Product.objects.filter(filter_query)
        elif brand_id:
            filter_query = Q(brand__id=brand_id)
            products = Product.objects.filter(filter_query)
        else:
            products = Product.objects.all()

         categories = Category.objects.all()
         brands = Brand.objects.all()
         context = {
            'products': products,
            'categories': categories,
            'brands': brands,
            'search_query': '',
            }
         return render(request, 'index.html', context)
    elif request.method == "POST":
        q = request.POST.get("query")   
        if "-" in q:
            price_values = q.split("-")
            filter_query = Q(price__gte=price_values[0]) & Q(price__lte=price_values[1])
        else:
            filter_query = Q(name__icontains=q) | Q(price__icontains=q) | Q(brand__name__icontains=q)
            products = Product.objects.filter(filter_query)
            categories = Category.objects.all()
            brands = Brand.objects.all()
            context = {
                    'products': products,
                    'categories': categories,
                    'brands': brands,
                    'search_query': q,
                }
            return render(request, 'index.html', context)

5.In “product_module” create a file for “urls.py” and add the URL routing config.
        from django.urls import path
        from .views import index
urlpatterns = [
        path('', index),
]

6.In “ecommerce_yourname > urls.py”, include “product_module.urls”
from django.contrib import admin
from django.urls import path, include
        urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('product_module.urls')),
]

7.Run the project and navigate to admin to check the result.
        python manage.py runserver

8.Go to browser and navigate your project url.
a. In browser, open 'http://127.0.0.1:8000/admin' for admin interface
b. Add records for brands, categories and products.
c. In browser, open 'http://127.0.0.1:8000 for user interfrace (search)
d. Perform various combination for search operation

B.Output:
![web2](https://user-images.githubusercontent.com/104376833/174480825-9fd69f18-8f2a-4a34-ae57-903da4b9d097.png)

C.Conclusion:
    Hence we can conclude that Lab 4 helps in searching Products and it's details.
