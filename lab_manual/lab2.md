   Lab 2

   Adding Product, Brand, Category and Color

   1.Objective:The objective of this lab is to add product,brand,category and color module.


   2.Introduction:
        A.Python:Python is a general-purpose, high-level, interpreted programming language. Its design philosophy prioritizes code readability through extensive indentation.Python is garbage-collected and dynamically typed. It works with a variety of programming paradigms, including structured (especially procedural), object-oriented, and functional programming.

        B.Django:Django is a Python web framework that promotes rapid development and clean, pragmatic design. It was built by experienced developers to take care of a lot of the work of web development so you can focus on building your app instead of reinventing the wheel. It's open source and free.
    

    3.Procedure:
        A.Adding a module product_module
        python manage.py startapp product_module

        B.Go to settings.py and add the module to INSTALLED_APPS list
        INSTALLED_APPS = [ ...,
        'product_module' ]

        C.In the module, open “models.py” and add code for Brand model

        class Brand(models.Model):
        name = models.CharField(max_length=200)
        is_active = models.BooleanField()

        D.Ensure database table for added model is created properly
        python manage.py makemigrations
        python manage.py migrate

        E.In database tool, verify that the added table is created properly

        F.Goto “admin.py” and add the following code
        from .models import Brand   
        admin.site.register(Brand)

        G.Run the server and verify CRUD operation
        python manage.py runserver
        a. In browser, open 'http://127.0.0.1:8000/admin'
        b. Perform: create, read, update, delete operation

        H.In the product_module module, open “models.py” and edit code for Brand model
        class Brand(models.Model):
        name = models.CharField(max_length=200)
        is_active = models.BooleanField()

       I.In the product_module module, open “models.py” and edit code for Category model
        class Category(models.Model):
        name = models.CharField(max_length=200)
        is_active = models.BooleanField()
        class Meta:
        verbose_name_plural = "Categories"

        J.In the product_module module, open “models.py” and edit code for Product model.
        Notice the following:
        • Each model inherits “models.Model”.
        • Use of ForeignKey for one-to-many relationship.
        • Use of max_length in CharField
        • Use of on_delete=models.CASCADE for ForeignKey
        • Use of CharField, FloatField, IntegerField, DateTimeField, BooleanField,ForeignKey

        K.class Product(models.Model):
          name = models.CharField(max_length=200)
          price = models.FloatField()
          quantity = models.IntegerField()
          image_url = models.CharField(max_length=500)
          color_code = models.CharField(max_length=20)
          brand = models.ForeignKey(Brand, on_delete=models.CASCADE)
          category = models.ForeignKey(Category, on_delete=models.CASCADE)
          registered_on = models.DateTimeField()
          is_active = models.BooleanField()

        L.By default, sqlite3 is the default database used in Django.
          Sample database configuration (for sqlite3):
          DATABASES = {
         'default': {
         'ENGINE': 'django.db.backends.sqlite3',
         'NAME': BASE_DIR / 'db.sqlite3',
         }
         } 

         M.Run migration commands to persist the changes to db.
         python manage.py makemigrations
         python manage.py migrate

         N.Create a superuser for admin interface
         python manage.py createsuperuser

         O.To enable the admin interface, go to “admin.py” and add the following code. This enabled the   CRUD operation for the corresponding model in admin site.
         from .models import Brand, Category, Product
         admin.site.register(Brand)
         admin.site.register(Category)
         admin.site.register(Product)

         P.Run the project/server and verify CRUD operation for brand, category, product
         python manage.py runserver

         Q.Go to browser and navigate your project url.
         a.In browser, open 'http://127.0.0.1:8000'. This is the normal user mode
         b.In browser, open 'http://127.0.0.1:8000/admin' for admin interface
         c.Perform: create, read, update, delete operation for brand
         d.Perform: create, read, update, delete operation for category
         e.Perform: create, read, update, delete operation for product


      4.Conclusion:Hence the above code shows adding product brand,category and product.