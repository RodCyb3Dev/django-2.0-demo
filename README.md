Third-party library support for older version of Django.

Following the release of Django 2.0, we suggest that third-party app authors drop support for all versions of Django prior to 1.11. At that time, you should be able to run your package’s tests using python -Wd so that deprecation warnings do appear. After making the deprecation warning fixes, your app should be compatible with Django 2.0.

# What’s new in Django 2.0
Simplified URL routing syntax
The new django.urls.path() function allows a simpler, more readable URL routing syntax. For example, this example from previous Django releases: https://docs.djangoproject.com/en/2.0/releases/2.0.1/ 
OR 
Django 2.1 release notes - UNDER DEVELOPMENT.
https://docs.djangoproject.com/en/dev/releases/2.1/
One of the Changes is;
Simplified URL routing syntax. 
The new django.urls.path() function allows a simpler, more readable URL routing syntax. 
For example, 
this example from previous Django releases:<br>
$ url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive), 
could be written as:<br>
$ path('articles/<int:year>/', views.year_archive),

# Lets Try

        HOW TO INSTALL DJANGO             
            STEP BY STEP                 
     
$ mkdir project-folder<br>              
$ cd project-folder<br>
$ easy_install pip  (If you don't ready have it.)<br>
$ pip install virtualenv<br>
$ virtualenv tryTen<br>
$ cd tryTen

To Activate The Virtual Env<br>
OSX/Linux<br>
$ source bin/activate<br>

On Windows<br>
$ .\Scripts\activate

Running pip freeze will allow you to see the file on your VirtualEnv
$ pip freeze
$ ls

Now you Can Install Django
$ pip install django==2.0

Then Run
$ pip freeze ( **Notice that now it in Says Django==2.0** )

To Create Your Project Run
$ django-admin.py startproject AddName
$ cd AddName
$ ls

To Create Apps in the Project
$ django-admin.py startapp AppName

Here you should see manage.py file that means you'r in the root dir







