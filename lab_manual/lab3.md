LAB 3
            Search Products (Admin)
            
1.Enhance the admin for search enhancements. Open “admin.py”.
Replace

    admin.site.register(Product)
    with
    class ProductAdmin(admin.ModelAdmin):
    list_display = ["name", "price", "brand", "category",]
    search_fields = ["name", "price", "brand__name", "category__name",]
    list_filter = ["brand","category",]
    readonly_fields = ["quantity",]
    
    class Meta:
        model = Product
        admin.site.register(Product, ProductAdmin)

2.Make similar changes to other models, e.g. Brand, Category, etc.

3.Run the project and navigate to admin
    python manage.py runserver
    a.Browse 'http://127.0.0.1:8000/admin' for seach/list operation

4.To display image in list view, first add a field “image_tag” to Product class in ‘models.py’.

    from django.utils.html import mark_safe

    class Product(models.Model):

    def image_tag(self):
        return mark_safe(f'<img src="{self.image_url}" width="50" height="50" />')
        image_tag.short_description = "Product"

    def __str__(self):
        return self.name

5.Let’s display image to list view. Go to 'admin.py' and modify the ProductAdmin as below:

    class ProductAdmin(admin.ModelAdmin):
        list_display = ["image_tag", "name", "price", "brand", "category",]
        search_fields = ["name", "price", "brand__name", "category__name",]
        list_filter = ["brand","category","price",]
        # readonly_fields = ["quantity",]
            class Meta:
                model = Product
    admin.site.register(Product, ProductAdmin)

6.Run the project and navigate to admin to check the result.    
    python manage.py runserver

7.Go to browser and navigate your project url.
    a.In browser, open 'http://127.0.0.1:8000/admin' for admin interface
    b.Perform: various combination of search and filter operations
    c.Check out the usage of readonly_fields

8.In the database tool, verify the database, tables and records.

