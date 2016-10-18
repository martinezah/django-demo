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
```pip install django
django-admin startproject webapp
cd webapp
./manage.py runserver
```

*Note the error message, which we'll talk about later:* `You have 13 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.`

Visit http://localhost:8000/

## Creating a "Hello, world" view

1) edit `webapp/views.py`
    
    Add a `hello` controller
    
    ```from django.conf import settings
    from django.shortcuts import render

    def hello(request):
        response = { 
            'username': 
                request.user.username if request.user.is_authenticated 
                else 'World' 
        }
        return render(request, 'hello.html', response)
    ```

1) edit `webapp/settings.py`

    Add `'webapp'` to `INSTALLED_APPS`

1) edit `webapp/templates/hello.html`
    ```
    <!DOCTYPE html>
    <html>
    <body>
    Hello, {{ username }}!
    </body>
    </html>
    ```

1) edit `webapp/urls.py`
    
    Import `views` and add `url(r'^$', views.hello, name='hello')` to `urlpatterns`
    
Now revisit http://localhost:8000/

