# REST-APIs-Django

## Create Anaconda Environment
`conda create -n Django_env python==3.8`

## Installation
`pip install django`
`pip install djangorestframework`

## Start a new project
```
django-admin startproject mysite
cd mysite/
python manage.py runserver
```
Go to localhost:8000 and you should see the Django welcome screen!

## Create API app
`python manage.py startapp myapi`
Register the myapi app with the mysite project
mysite/settings.py :
```
INSTALLED_APPS = [
    'myapi.apps.MyapiConfig',
    ... # Leave all the other INSTALLED_APPS
]
```

## Migrate the database
`python manage.py migrate`

## Create Super User
`python manage.py createsuperuser`
`python manage.py runserver`
And then navigate to localhost:8000/admin

## Build model
Create a model in the database that Django ORM will manage
myapi/models.py

models.py
```
from django.db import models
class Hero(models.Model):
    name = models.CharField(max_length=60)
    alias = models.CharField(max_length=60)
    def __str__(self):
        return self.name
```
## Make migrations
`python manage.py makemigrations`
`python manage.py migrate`

Register model with the admin site
```
from django.contrib import admin
from .models import Hero
admin.site.register(Hero)
```
Now run the Django server:
`python manage.py runserver`

## Set up Django REST Framework
mysite/settings.py:
```
INSTALLED_APPS = [
    # All your installed apps stay the same
    ...
    'rest_framework',
]
```

## Serialize the Hero model
serializers.py
```
from rest_framework import serializers
from .models import Hero

class HeroSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Hero
        fields = ('name', 'alias')
```

## Display the data
views.py

```
from rest_framework import viewsets
from .serializers import HeroSerializer
from .models import Hero


class HeroViewSet(viewsets.ModelViewSet):
    queryset = Hero.objects.all().order_by('name')
    serializer_class = HeroSerializer
```

## Site URLs
mysite/urls.py
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapi.urls')),
 ]
 ```

myapi/urls.py
```
from django.urls import include, path
from rest_framework import routers
from . import views

router = routers.DefaultRouter()
router.register(r'heroes', views.HeroViewSet)

# Wire up our API using automatic URL routing.
# Additionally, we include login URLs for the browsable API.
urlpatterns = [
    path('', include(router.urls)),
    path('api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
```

## Execute
Start up the Django server again:
`python manage.py runserver`
Now go to localhost:8000

## Reference
- https://medium.com/@BennettGarner/build-your-first-rest-api-with-django-rest-framework-e394e39a482c
