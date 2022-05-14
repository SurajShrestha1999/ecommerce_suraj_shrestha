    Lab 1
    Introduction to Python/Django Framework

    1.Objective:The objective of this lab is to prepare your environment, and run a simple project.

    2.Introduction
        A.Python:Python is a general-purpose, high-level, interpreted programming language. Its design philosophy prioritizes code readability through extensive indentation.Python is garbage-collected and dynamically typed. It works with a variety of programming paradigms, including structured (especially procedural), object-oriented, and functional programming. Because of its extensive standard library, it is often referred to as a "batteries included" language.

        B.Django:Django is a Python web framework that promotes rapid development and clean, pragmatic design. It was built by experienced developers to take care of a lot of the work of web development so you can focus on building your app instead of reinventing the wheel. It's open source and free.

        C.Visual Studio Code:Microsoft Visual Studio is the company's integrated development environment (IDE).It's used to make websites, web apps, web services, and mobile apps, among other things. Windows API, Windows Forms, Windows Presentation Foundation, Windows Store, and Microsoft Silverlight are among the Microsoft software development platforms used by Visual Studio. It can generate native as well as managed code.

        3.Procedure 
            python --version

            django-admin startproject ecommerce_django_project
            cd ecommerce django_project

            python manage.py migrate
            python manage.py createsuperuser

            python manage.py runserver
            a.Verify frontend in browser: Open http://127.0.0.1:8000
            b.Verify admin backend in browser: Open http://127.0.0.1:8000/admin

        4.Conclusion
            We can conclude that Lab 1 provides the knowledge of Python3/Django Framework and Visual Studio Code.



