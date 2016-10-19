# Intro to Django 

## What is Django?

- Python Web Framework
    - Follows the Model/View/Controller design pattern
    - Open source, BSD licensed
- Pluggable Components
   - Template Engine (inspiration for Jinja syntax)
   - ORM (works with SQLite, MySQL, Postgres, etc)
   - Schema migration engine
   - User/Group-based permissions
   - Admin UI

## Project Quickstart

    pip install django
    django-admin startproject webapp
    cd webapp
    ./manage.py runserver

*Note the error message, which we'll talk about later:* `You have 13 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.`

Visit <http://localhost:8000/>

## Creating a "Hello, world" view

1) edit `webapp/views.py`
    
Add a `hello` controller
    
    from django.conf import settings
    from django.shortcuts import render

    def hello(request):
        response = { 
            'username': 
                request.user.username if request.user.is_authenticated 
                else 'World' 
        }
        return render(request, 'hello.html', response)

2) edit `webapp/settings.py`

Add `'webapp'` to `INSTALLED_APPS`

3) edit `webapp/templates/hello.html`

    <!DOCTYPE html>
    <html>
    <body>
    Hello, {{ username }}!
    </body>
    </html>

4) edit `webapp/urls.py`
    
Import `views` and add `url(r'^$', views.hello, name='hello')` to `urlpatterns`
    
Now revisit <http://localhost:8000/>

## Template Inheritance

1) Create `webapp/templates/base.html`

    <!DOCTYPE html>
    <html>
    <body>
    {% block content %}
    Nothing to see here
    {% endblock %}
    </body>
    </html>

2) Edit `webapp/templates/hello.html`

    {% extends 'base.html' %}
    {% block content}
    Hello, {{ username }}!
    {% endblock %}
 
## Models and Admin UI

Recall that we got an error message about `unapplied migrations` when we first started the development server. Django has a built-in ORM that manages database schemas, and a default project includes several core Django "apps" that have associated schemas. 

1) Add those initial schemas to the database:

`./manage.py migrate`

2) Create an admin superuser account:

`./manage.py createsuperuser`

3) Visit <http://localhost:8000/admin>

4) Create `webapp/models.py`

    from django.contrib.auth.models import User
    from django.db import models
    
    class Note(models.Model):
        user = models.ForeignKey(User)
        created = models.DateTimeField()
        title = models.CharField(max_length=255)
        text = models.TextField()

5) Generate schema migration

`./manage.py makemigrations webapp`

6) Apply the migrations

`./manage.py migrate`

7) Create `webapp/admin.py`

    from django.contrib import admin
    from .models import *

    admin.site.register(Note)

8) Restart the development server, and revisit <http://localhost:8000/admin>

9) Use the Admin UI to add a couple of Note objects

10) Add a `__str__()` method to Note model if desired

## Working with Models and Views

1) Add to `hello()` in `webapp/views.py`:

    response['notes'] = Note.objects.filter(user=request.user)

2) Add to `webapp/templates/hello.html`:
    
    {% for note in notes %}
    <h4><a href="{% url "note" note.id %}">{{ note.title}}</a></h4>
    {% endfor %}

Revisit <http://localhost:8000/> 

3) Add a route to `webapp/urls.py`:
    
    url(r'^(?P<id>[0-9]+)/?$', views.note, name='note')

4) Add a view to `webapp/views.py`:

    def note(request, id):
        response = {
            'note': Note.objects.get(id=id)
        }
        return render(request, 'note.html', response)
        
5) Add a template `webapp/templates/note.html`:
    
    {% extends 'base.html' %}
    {% block content %}
    <h4>{{note.title}}</h4>
    <p>{{ note.text }}</p>
    {% endblock %}

## Next steps

- REST Extensions <http://www.django-rest-framework.org>
- Unit Testing <https://docs.djangoproject.com/en/1.10/topics/testing/overview/>
- Authentication & Permissions <https://docs.djangoproject.com/en/1.10/topics/auth/default/>
