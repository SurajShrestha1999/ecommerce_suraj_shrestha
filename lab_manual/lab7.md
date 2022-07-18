Checkout Preparation and Security
PART I: CHECKOUT PREPARATION
1.We are going to define our own payment

2.Let’s add a module payment_module
python manage.py startapp payment_module

3.Go to settings.py and add the module to INSTALLED_APPS list
    INSTALLED_APPS = [ ...,
        'payment_module' ]

4.In the payment_module, open “models.py” and add code for payment_gateway.
import uuid
class PaymentGateway(models.Model):
    token = models.UUIDField(default= uuid.uuid4,editable=False)
    expiry_date = models.DateField()
    balance = models.FloatField()
    is_active = models.BooleanField()

5.Ensure database table for added model is created properly
A.python manage.py makemigrations payment_module
B.python manage.py migrate payment_module

6.In database tool, verify that the added table is created properly

7.Goto “admin.py” and add the following code
from .models import PaymentGateway
class PaymentGatewayAdmin(admin.ModelAdmin):
    list_display = ["token", "balance", "expiry_date", "is_active",]
    
    class Meta:
        model = PaymentGateway
admin.site.register(PaymentGateway, PaymentGatewayAdmin)

8.Run the server and generate token for payment
python manage.py runserver
A.In browser, open 'http://127.0.0.1:8000/admin'
B.Add a record for payment gateway and make note of the token. We will need token for making payments.