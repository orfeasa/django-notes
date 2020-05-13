# Django Notes

There are the notes I was taking while learning Django. Feel free to contribute!

- [Django Notes](#django-notes)
  - [Django Setup](#django-setup)
    - [Activate your virtual environment (optional)](#activate-your-virtual-environment-optional)
    - [Create a project](#create-a-project)
    - [Create an application](#create-an-application)
    - [Create templates folder](#create-templates-folder)
    - [Create any HTML templates for your pages](#create-any-html-templates-for-your-pages)
  - [Creating models](#creating-models)
    - [Let Django know where the templates are](#let-django-know-where-the-templates-are)
    - [Also add your static and media folders](#also-add-your-static-and-media-folders)
    - [Add the app's models](#add-the-apps-models)
    - [Add the app's views](#add-the-apps-views)
    - [Set up app URL files](#set-up-app-url-files)
    - [Edit project URL files](#edit-project-url-files)
  - [Migrate and manage models via admin](#migrate-and-manage-models-via-admin)
    - [Migrate models to database](#migrate-models-to-database)
    - [Register model to the admin interface](#register-model-to-the-admin-interface)
    - [Create a super user](#create-a-super-user)
    - [Creating fake data for our models (optional)](#creating-fake-data-for-our-models-optional)
  - [Creating forms](#creating-forms)
    - [Create the form class](#create-the-form-class)
    - [Show the form with a view](#show-the-form-with-a-view)
    - [Add the view to the app's urls](#add-the-view-to-the-apps-urls)
    - [Inject content from the form to the html page](#inject-content-from-the-form-to-the-html-page)
    - [Accessing data](#accessing-data)
    - [Form validation](#form-validation)
      - [Check for empty fields](#check-for-empty-fields)
      - [Creating your own custom validators](#creating-your-own-custom-validators)
      - [Clean entire form](#clean-entire-form)
    - [Model Forms](#model-forms)
  - [Relative URLs](#relative-urls)
    - [App's relative URL](#apps-relative-url)
    - [Linking to the admin page](#linking-to-the-admin-page)
    - [Linking to the index page](#linking-to-the-index-page)
    - [Template inheritance](#template-inheritance)
  - [Django template filters](#django-template-filters)
    - [Creating custom filters](#creating-custom-filters)
  - [User Authentication](#user-authentication)
    - [Passwords](#passwords)
    - [User Models](#user-models)
    - [Login](#login)
  - [Class-Based Views (CBVs)](#class-based-views-cbvs)
    - [Show basic view](#show-basic-view)
    - [Using TemplateView](#using-templateview)
    - [Detail View and List View](#detail-view-and-list-view)
      - [ListView](#listview)
      - [DetailView](#detailview)
    - [CRUD](#crud)
      - [CreateView class](#createview-class)
      - [UpdateView class](#updateview-class)
      - [DeleteView class](#deleteview-class)
  - [Other notes](#other-notes)

## Django Setup

### Activate your virtual environment (optional)

```console
conda activate <environment_name>
```

### Create a project

To start the project run in the console

```console
django-admin startproject <project_name>
```

### Create an application

```console
cd <project_name>
django-admin startapp <app_name>
```

### Create templates folder

```console
mkdir templates\<app_name>
```

### Create any HTML templates for your pages

For example `index.html` and `appPage.html`

## Creating models

### Let Django know where the templates are

Go to `<project_name>/<project_name>/settings.py`.
Under the BASE_DIR declaration create `TEMPLATE_DIR`:

```python
TEMPLATE_DIR = os.path.join(BASE_DIR, "templates")
```

add the new app in the `INSTALLED_APPS` variable:

```python
INSTALLED_APPS = [
    # ...
    '<app_name>',
]
```

and add the new variable in the `'DIRS'` key, at the end:

```python
TEMPLATES = [
    {
    # ...
    'DIRS': [TEMPLATE_DIR, ],
    # ...
            ],
        },
    },
]
```

### Also add your static and media folders

In a similar way, add the static and media folder to your `settings.py` file:

```python
# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates')
STATIC_DIR = os.path.join(BASE_DIR, 'static')
MEDIA_DIR = os.path.join(BASE_DIR, 'media')
```

And at the end of the file, your `settings.py` should look like this:

```python
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/3.0/howto/static-files/

STATIC_URL = '/static/'
STITECFILES_DIRS = [STATIC_DIR, ]

# Media files
MEDIA_ROOT = MEDIA_DIR
MEDIA_URL = '/media/'
```

### Add the app's models

Open `<project_name>/<app_name>/models.py` and add your models there.

```python
class <Model_name>(models.Model):
    #...
```

### Add the app's views

Change `<app_name>/views.py` to:

```python
from django.shortcuts import render
from .models import <Model_name>

def index(request):
    return render(request, "<app_name>/index.html")

# if I want another view
def <app_view>(request):
    <model_list> = <Model_name>.objects.order_by('<field_1>')
    <model_dict> = {'<model_name>': <model_list>}
    return render(request, "<app_name>/appPage.html", context=<model_dict>)
```

### Set up app URL files

Create the file `<appname>/urls.py` and add the following. `views.<app_page>` is the function we created before, and `appPage` is the `appPage.html` created earlier.

```python
from django.conf.urls import url
from django.urls import path
from . import views

urlpatterns = [
    path("", views.<app_view>,name="appPage"),
]
```

### Edit project URL files

Edit `<project_name>/urls.py` (don't forget to also import include, as shown in line 2):

```python
from django.contrib import admin
from django.urls import include, path
from <app_name> import views

urlpatterns = [
    path('', views.index, name="index"),
    path('<app_name>/', include('<app_name>.urls')),
    path('admin/', admin.site.urls),
]
```

## Migrate and manage models via admin

### Migrate models to database

Time to see if everything is set up correctly.

```console
python manage.py migrate
python manage.py makemigrations <app_name>
python manage.py migrate
```

Always do that after adding an app to your project.

### Register model to the admin interface

```python
from django.contrib import admin
from .models import <Model_name>

# Register your models here.
admin.site.register(<Model_name>)
```

### Create a super user

```console
python manage.py createsuperuser
```

To make sure everything works right, run

```console
python manage.py migrate
```

You should get `No migrations to apply.`

### Creating fake data for our models (optional)

To create fake data using the faker module, create and run a python script like the one below.

```python
import os
os.environ.setdefault("DJANGO_SETTINGS_MODULE", 'ProTwo.settings')

import django
django.setup()

from appTwo.models import User
from faker import Faker

fakegen = Faker()

def populate(N=5):
    for _ in range(N):
        fake_name = fakegen.name().split()
        fake_first_name = fake_name[0]
        fake_last_name = fake_name[1]
        fake_email = fakegen.email()

        # New entry
        user = User.objects.get_or_create(
            first_name=fake_first_name,
            last_name=fake_last_name,
            email=fake_email
        )[0]

if __name__ == '__main__':
    print("POPULATING DATABASES")
    populate(20)
    print("COMPLETE")
```

## Creating forms

### Create the form class

Create a `forms.py` in the application folder:

```python
from django import forms

class FormName(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    text = forms.CharField(widget=forms.Textarea)
```

You can check get more info about widgets from [the documentation](https://docs.djangoproject.com/en/3.0/ref/forms/widgets/).

### Show the form with a view

Inside the app's `views.py` file, import the forms using one of the following ways:

```python
from . import forms
```

or

```python
from forms import FormName
```

Once the form is imported you can create a new view for it:

```python
def form_name_view(request):
    form = forms.FormName()
    return render(request, '<app_name>/form_name.html', {'form':form})
```

### Add the view to the app's urls

This can be done either directly or with `include()`
Directly:

```python
from django.urls import path
from <app_dir> import views

urlpatterns = [
    #...
    path("formpage/", views.form_name_view, name="form_name")
]
```

### Inject content from the form to the html page

All you really need is to use the key from the context dictionary, so `{{ form }}`.
A more complete example could look like this.

```html
<div class="container">
  <form method="POST">
    {{ form.as_p }} {% csrf_token %}
    <input type="submit" class="btn btn-primary" value="Submit" />
  </form>
</div>
```

### Accessing data

You can access the data with `form.cleaned_data["field"]`
Inside your app's `views.py` file:

```python
def form_name_view(request):
    form = forms.formName()

    # Check to see if we gat a POST back
    if request.method == "POST":
        # In which case we pass in that request
        form = forms.FormName(request.POST)

        # Check to see if form is valid
        if form.is_valid():
            # use input
            user_name = form.cleaned_data['name']

    return render(request, 'basicapp/form_page.html', {'form':form})
```

### Form validation

We will be working inside our app's `forms.py` file.

We are already in the state

```python
from django import forms

class FormName(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    text = forms.CharField(widget=forms.Textarea)
```

#### Check for empty fields

```python
class FormName(forms.Form):
    # ...
    botcatcher = forms.CharField(
        required=False,
        widget=forms.HiddenInput
    )

```

We will see first the manual way, then the automatic way that Django has already implemented.
We create a function inside the class, named `clean_<variable_name>`, so in our case:

```python
class FormName(forms.Form):
    # ...

    def clean_botcatcher(self):
        botcatcher = self.cleaned_data["botcatcher"]
        if len(botcatcher) > 0:
            raise forms.ValidationError("Gotcha Bot!")
        return botcatcher
```

Using Django's built-in validators

```python
from django import forms
from django.core import validators

class FormName(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    text = forms.CharField(widget=forms.Textarea)
    botcatcher = forms.CharField(
        required=False,
        widget=forms.HiddenInput
        validators = [validators.MaxLengthValidator(0)]
    )

```

#### Creating your own custom validators

Create a function outside your class

```python
#...
def check_for_z(value):
    if value[0].lower() != 'z'
        raise forms.ValidationError("Name needs to start with Z")


class FormName(forms.Form):
    name = forms.CharField(validators=[check_for_z])
    #...
```

#### Clean entire form

```python
#...
class FormName(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    verify_email = forms.EmailField(label="Enter your email again:")
    text = forms.CharField(widget=forms.Textarea)

    def clean(self):
        all_clean_data = super().clean()
        email = all_clean_data["email"]
        vmail = all_clean_data["verify_email"]

        if email != vmail:
            raise forms.validationError("Make sure emails match!")
```

### Model Forms

To save form info to a model, instead of inheriting from `forms.Form` class, we can use `forms.modelForm` in our `forms.py` file.
We will need to use a Meta class inside the MyNewForm class, to connect the model to the form.

Example:

```python
from django import forms
from myapp.models import MyModel

class MyNewForm(forms.ModelForm):
    # Form Fields go here (only if you want custom validators)

    class Meta:
        model = MyModel
        fields = # let's see
```

Regarding the `fields`:

- Set it to `fields = "__all__"`, to grab all the fields from the model and place them in the form
- Select the fields to exclude, by `exlcude = ["field1", "field2"]`
- Select the fields to include, `fields = ("field1", "fields2")`

## Relative URLs

### App's relative URL

In the app's `urls.py` file, create a variable called `app_name` and assign it the name of the app

```python
from django.conf.urls import url, include
from basic_app import views
from django.urls import path

# TEMPLATE TAGGING
app_name = "basic_app"

urlpatterns = [
    path("relative/", views.relative, name="relative"),
    path("other/", views.other, name="other")
]
```

In your HTML file, to create a relative path you can then do it using URL template tagging:

```html
<a href="{% url 'basic_app:other' %}">The other page</a>
```

The `basic_app` needs to match the variable assigned to `app_name` in `urls.py`

_Note_: A common error is leaving a leading or trailing space in the string `'basic_app:other'` (leaving it `' basic_app:other'` or `'basic_app:other '`). This is a tricky error to solve because django will be looking either for the `' basic_app'` app or for the `'other '` url pattern name, and will report "cannot find other ", whereas "other", but not "other " is already there.

### Linking to the admin page

To link to the admin page you can use

```html
<a href="{% url 'admin:index' %}">The admin page</a>
```

There is an app already imported in the `settings.py` file. You usually need to make the migration before this works, so remember to run

```console
python manage.py migrate
```

### Linking to the index page

All you need to do is:

```html
<a href="{% url 'index' %}">The index page</a>
```

Again remember not to leave any leading/trailing spaces.

### Template inheritance

Find the repetitive parts of the project
Create a base template of them
Set the tags in the base template
Extend and call those tags anywhere

The first line of any HTML file should be

```html
<!DOCTYPE html>
```

In your "base" HTML file, write what code you want all pages to have. At a certain point, add the following code:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <!-- ... -->
  </head>
  <body>
    <!--  navbar/header code-->
    <div class="container">
      {% block body_block %}
      <!-- Anyhting outside of this will be inherited if you extend! -->
      {% endblock %}
    </div>
    <!-- scripts loading -->
  </body>
</html>
```

Eveything before and after this block will be inherited.

So, to inherit that code in another page, use the following code:

```html
<!DOCTYPE html>
{% extends "basic_app/base.html" %} {% block body_block %}
<!-- Page-specific content here -->
{% endblock %}
```

## Django template filters

Link to [the documentation](https://docs.djangoproject.com/en/3.0/topics/templates/#filters)

General form of template filter:

```html
{{ value | filter:"parameter"}}
```

### Creating custom filters

Before creating your own filter, search the ones already available, most probably there is something there for your use case.

As a convention, inside your application folder create a new directory called `templatetags`. Inside that directory, create the empty file `__init__.py` to let python know this can be imported (maybe this is not needed anymore).
In the same directory you can now create a python file with your filters.

In the new file, you can register your custom filters in the following way:

```python
from django import template

register = template.Library()

def cut_function_name(value, arg):
    """
    This cuts out all values of "arg" from the string!
    """
    return value.replace(arg, '')

register.filter("cut2", cut_function_name)
```

Or, replacing the last line with a decorator, before the fuction call the file would look like this:

```python
# ...
@register.filter(name="cut2")
def cut_function_name(value, arg):
    # function code
```

At the top of your template/html file, you need to add a load call:

```python
{% load my_templates %}
```

Where you would replace my_templates with whatever you decided to call your .py file containing the extra template tags. This is the .py file that is directly underneath the templatetags folder.

The server needs to be restarted for changes to take effect.

## User Authentication

### Passwords

First we will use some Django built-in apps, which we need to add to the `INSTALLED_APPS` in our `settings.py` file. The apps we will use are `django.contrib.auth` and `django.contrib.contenttypes`.
Remember to migrate if you added them.

We will start with the default PBKDF2 algorithm with a SHA256 hash.
For more security you can use bcrypt and Argon 2. To install them run:

```console
pip install bcrypt
pip install django[argon2]
```

Inside `setting.py` then pass the list of `PASSWORD_HASHERS` in the order you want to try them.

You can also add validators to prevent easy passwords.

After installing bcrypt and Argon 2, edit the `settings.py` file by adding the following:

```python
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
    'django.contrib.auth.hashers.BCryptPasswordHasher',
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
]

AUTH_PASSWORD_VALIDATORS = [
    # ...
```

The `AUTH_PASSWORD_VALIDATORS` contains the validators that will be used for your users' passwords. Each of those may also have options, which you add with another key in the dictionary, for example, you can add a minimum length of 9 by adding the line `'OPTIONS': {'min_length':9}`:

```python
AUTH_PASSWORD_VALIDATORS = [
    #  ...
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        'OPTIONS': {'min_length':9}
    },
    # ...
]
```

### User Models

The Django built-in `User` object already has a few key features (username, email, password, first & last name, is_active, is_staff, is_superuser).
You can add attributes to a user by creating another class tha has a relationship to the `User` class.
In the `models.py` file you could do the following:

```python
from django.db import models
from django.contrib.auth.models import User

# Create your models here.
class UserProfileInfo(models.Model):

    # Create relationship (don't inherit from User!)
    user = models.OneToOneField(User, on_delete=models.CASCADE)

    # Add any additional attributes you want
    portfolio_site = models.URLField(blank=True)
    profile_pic = models.ImageField(upload_to='basic_app/profile_pics',blank=True)

    def __str__(self):
        # Built-in attribute of django.contrib.auth.models.User !
        return self.user.username
```

`blank=True` means the user doesn't have to fill it our, there won't be any errors if it's empty.
To use the profile pic you will need to install pillow:

```console
pip install pillow
```

or, if the above doesn't work you may need to run:

```console
pip install pillow --global-option="build_ext" --global-option="--disable-jpeg"
```

And remember to register the model in the `admin.py` file with something like:

```python
admin.site.register(UserProfileInfo)
```

Now when setting up the registration page:

- You will typically need to use `{% load static %}` at the top of the page, to load the images and branding of the registeration page
- In the actual form, you'll need to specify the enctype as follows for the image upload to work: `<form method="POST" enctype="multipart/form-data">`

Now when editing the app's `views.py` file, sometimes we want to save the information directly to the database. Other times we will set `commit=False` so we can manipulate the data before saving it to the database. This helps prevent collision errors.

### Login

The process involves:

- Setting up login views
- Using built-in decorators for access
- Adding `LOGIN_URL` in settings
- Creating the `login.html`
- Editing the `urls.py` files

First we go to `settings.py` and add the login URL, at the end of the file:

```python
LOGIN_URL = '/basic_app/user_login'
```

Then we have to setup our HTML files, and in the login page we use the login action `user_login`:

```html
<form action="{% url 'basic_app:user_login' %}" method="post"></form>
```

Most of the work for the log in happens at the `views.py` file.
First we have the extra imports for the log in:

```python
# Extra Imports for the Login and Logout Capabilities
from django.contrib.auth import authenticate, login, logout
from django.http import HttpResponseRedirect, HttpResponse
from django.urls import reverse
from django.contrib.auth.decorators import login_required
```

If you want a view to require a user to be logged in, you can use the `login_required` decorator. Django has a lot of authorizaiton decorators.

Make sure not to name your function `login`, as you are already importing it. You can use `user_login` instead.

```python
def user_login(request):

    if request.method == 'POST':
        # First get the username and password supplied
        username = request.POST.get('username')
        password = request.POST.get('password')

        # Django's built-in authentication function:
        user = authenticate(username=username, password=password)

        # If we have a user
        if user:
            #Check it the account is active
            if user.is_active:
                # Log the user in.
                login(request,user)
                # Send the user back to some page.
                # In this case their homepage.
                return HttpResponseRedirect(reverse('index'))
            else:
                # If account is not active:
                return HttpResponse("Your account is not active.")
        else:
            print("Someone tried to login and failed.")
            print(f"They used username: {username} and password: {password}")
            return HttpResponse("Invalid login details supplied.")

    else:
        #Nothing has been provided for username or password.
        return render(request, 'basic_app/login.html', {})
```

And then we'll add the logout page. This is very easy thanks to the decorator and Django's built-in logout function:

```python
@login_required
def user_logout(request):
    # Log out the user.
    logout(request)
    # Return to homepage.
    return HttpResponseRedirect(reverse('index'))
```

And remember to add the logout page in the project's `views.py` file:

```python

urlpatterns = [
    # ...
    path('logout/', views.user_logout, name='logout'),
    # ...
]
```

And in our application's `views.py` file add:

```python

urlpatterns = [
    # ...
    path('user_login/', views.user_login, name='user_login'),
]
```

Lastly, to display a login/logout link on the navbar depending of the user's log in state, we can use logic in the `base.html` as follows:

```html
<!-- body > navbar -->
{% if user.is_authenticated %}
<li class="nav-item">
  <a class="nav-link" href="{% url 'logout' %}">Logout</a>
</li>
{% else %}
<li class="nav-item">
  <a class="nav-link" href="{% url 'basic_app:user_login' %}">Log in</a>
</li>
{% endif %}
<!-- ... -->
```

## Class-Based Views (CBVs)

### Show basic view

To implement the most basic CBV, edit your app's `views.py` file to the following:

```python
from django.shortcuts import render
from django.views.generic import View
from django.http import HttpResponse

class CBView(View):
    def get(self, request):
        return HttpResponse("CBVs are cool!")
```

and in the `urls.py` add the line:

```python
#...
from django.urls import path
from basic_app.views import CBView

urlpatterns = [
    #...
    path('', CBView.as_view()),
]
```

### Using TemplateView

To use templateView you just inherit TemplateView in your class:
`views.py`

```python
from django.shortcuts import render
from django.views.generic import TemplateView

class IndexView(TemplateView):
    template_name = 'index.html'
```

To inject data from a context dictionary add a method to do so in the class, in `views.py`:

```python
class IndexView(TemplateView):
    template_name = 'index.html'
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['injectme'] = 'Basic injection!'
        return context
```

### Detail View and List View

To use ListView and DetailView, change the applicaiton's `views.py` file:

```python
from django.shortcuts import render
from django.views.generic import ListView, DetailView, TemplateView
from basic_app import models

class IndexView(TemplateView):
    template_name = 'index.html'

class SchoolListView(ListView):
    model = models.School
    context_object_name = 'schools'
    # this is in basic_app/templates/basic_app/
    template_name='basic_app/school_list.html'

class SchoolDetailView(DetailView):
    model = models.School
    context_object_name = 'school_detail'
    template_name = 'basic_app/school_detail.html'
```

#### ListView

The app's `urls.py` file should then have the pattern:

```python
urlpatterns = [
    path('', views.SchoolListView.as_view(), name='list'),
]
```

The respective html file (`school_list.html` for the ListView in our case) would look like:

```html
{% extends "basic_app/basic_app_base.html" %} {% block body_block %}

<h1>Welcome to a list of all the schools!</h1>
<ol>
  {% for school in schools %}
  <h2><li>{{school.name}}</li></h2>
  {% endfor %}
</ol>

{% endblock %}
```

If we don't define the `context_object_name` as `'schools'`, Django automaticall creates a method by converitng the name of the model to lowercase and appending `_list`. In our case this would be `school_list`.
For `DetaulView` models, the `context_object_name` is the model in lowercase by default (not appending `_list`)

#### DetailView

Now to show the detail view, we add the pattern in the `urls.py` file. `<int:pk>/` means that if the path is an int, treat is as a primary key for the `SchoolDetailView` (no primary key was specified, so one is created automatically).

```python
urlpatterns = [
    #...
    path('<int:pk>/', views.SchoolDetailView.as_view(), name='detail'),
]
```

For the detail view page, we can show the elements of the class, but also the students related to the school.
The connection from School to Student was done in `models.py` with the line:

```python
class School(models.Model):
    # ...

class Student(models.Model):
    # ...
    school = models.ForeignKey(School, related_name="students", on_delete=models.CASCADE)
```

Specifying the `related_name` field means we can then call the related object.
That's how we can iterate through the students below, calling `{% for student in school_detail.students.all %}`

```html
{% extends "basic_app/basic_app_base.html" %} {% block body_block %}

<div class="jumbotron">
  <h1>Welcome to the School Detail Page</h1>
  <h2>School Details</h2>
  <p>Name: {{school_detail.name}}</p>
  <p>Principal: {{school_detail.principal}}</p>
  <p>Location: {{school_detail.location}}</p>
  <h3>Students:</h3>
  {% for student in school_detail.students.all %}
  <p>{{student.name}} who is {{student.age}} years old.</p>
  {% endfor %}
</div>

{% endblock %}
```

### CRUD

CRUD stands for the basic actions that every website usually needs to do:

- **C**reate
- **R**etrieve
- **U**pdate
- **D**elete

So far we know how to Retrieve things by using the connection between the `urls.py`, `models.py` and `views.py` files.

#### CreateView class

#### UpdateView class

#### DeleteView class

## Other notes

- For beautiful forms, consider using `django-bootstrap4`
- For debugging check out the [Django Debug Toolbar](https://django-debug-toolbar.readthedocs.io/en/latest/index.html)
