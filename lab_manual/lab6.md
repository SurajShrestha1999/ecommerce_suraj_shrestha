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

5.