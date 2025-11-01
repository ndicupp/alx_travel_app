I'm going to set up the Django project with all the required components. Let's break this down step by step:

Step 1: Create Django Project and App
# Create project directory and navigate to it
mkdir alx_travel_app
cd alx_travel_app

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install django djangorestframework django-cors-headers celery django-celery-results django-environ drf-yasg mysqlclient

# Create Django project
django-admin startproject alx_travel_app .

# Create listings app
python manage.py startapp listings

Step 2: Configure Settings
First, create a .env file in the project root:
DEBUG=True
SECRET_KEY=your-secret-key-here
DB_NAME=alx_travel
DB_USER=root
DB_PASSWORD=yourpassword
DB_HOST=localhost
DB_PORT=3306

Now, update alx_travel_app/settings.py:
import os
from pathlib import Path
import environ

# Build paths inside the project like this: BASE_DIR / 'subdir'.
BASE_DIR = Path(__file__).resolve().parent.parent

# Initialize environment variables
env = environ.Env()
environ.Env.read_env(os.path.join(BASE_DIR, '.env'))

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = env('SECRET_KEY')

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = env.bool('DEBUG', default=False)

ALLOWED_HOSTS = ['*']

# Application definition
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    
    # Third party apps
    'rest_framework',
    'corsheaders',
    'drf_yasg',
    'django_celery_results',
    
    # Local apps
    'listings',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'alx_travel_app.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'alx_travel_app.wsgi.application'

# Database configuration
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': env('DB_NAME'),
        'USER': env('DB_USER'),
        'PASSWORD': env('DB_PASSWORD'),
        'HOST': env('DB_HOST', default='localhost'),
        'PORT': env('DB_PORT', default='3306'),
    }
}

# Password validation
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]

# Internationalization
LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'
USE_I18N = True
USE_TZ = True

# Static files (CSS, JavaScript, Images)
STATIC_URL = 'static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# Default primary key field type
DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'

# REST Framework configuration
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ],
    'DEFAULT_SCHEMA_CLASS': 'rest_framework.schemas.coreapi.AutoSchema',
}

# CORS configuration
CORS_ALLOW_ALL_ORIGINS = True
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "http://127.0.0.1:3000",
]

# Celery Configuration
CELERY_BROKER_URL = 'amqp://localhost'
CELERY_RESULT_BACKEND = 'django-db'
CELERY_ACCEPT_CONTENT = ['json']
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_TIMEZONE = 'UTC'

Step 3: Configure URLs with Swagger
Update alx_travel_app/urls.py:
from django.contrib import admin
from django.urls import path, include
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

# Swagger configuration
schema_view = get_schema_view(
    openapi.Info(
        title="ALX Travel App API",
        default_version='v1',
        description="API documentation for ALX Travel App",
        terms_of_service="https://www.google.com/policies/terms/",
        contact=openapi.Contact(email="contact@alxtravel.local"),
        license=openapi.License(name="BSD License"),
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/listings/', include('listings.urls')),
    
    # Swagger URLs
    path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
    path('redoc/', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
]

Step 4: Create Celery Configuration
Create alx_travel_app/celery.py:
import os
from celery import Celery

# Set the default Django settings module
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'alx_travel_app.settings')

app = Celery('alx_travel_app')

# Using a string here means the worker doesn't have to serialize
# the configuration object to child processes.
app.config_from_object('django.conf:settings', namespace='CELERY')

# Load task modules from all registered Django app configs.
app.autodiscover_tasks()

@app.task(bind=True)
def debug_task(self):
    print(f'Request: {self.request!r}')

Update alx_travel_app/__init__.py:
from .celery import app as celery_app

__all__ = ('celery_app',)

Step 5: Create Basic Listings App Structure
Create listings/urls.py:
from django.urls import path
from . import views

urlpatterns = [
    path('', views.listings_list, name='listings-list'),
]

Update listings/views.py:
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework import status

@api_view(['GET'])
def listings_list(request):
    """
    List all travel listings
    """
    return Response({
        "message": "Listings API is working!",
        "status": "success"
    })

Step 6: Create Requirements File and Git Setup
Create requirements.txt:
Django==4.2.7
djangorestframework==3.14.0
django-cors-headers==4.3.1
celery==5.3.4
django-celery-results==2.5.1
django-environ==0.10.0
drf-yasg==1.21.7
mysqlclient==2.2.0

Create .gitignore:
# Django
*.log
*.pot
*.pyc
__pycache__/
db.sqlite3
media/
staticfiles/

# Environment
.env
.venv
venv/
env/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Database
*.db

Step 7: Initialize Git Repository
# Initialize git
git init

# Add all files
git add .

# Make initial commit
git commit -m "Initial project setup: Django project with MySQL, REST framework, CORS, Celery, and Swagger documentation"

# Check the status
git status

Step 8: Create Project Documentation
Create README.md:
# ALX Travel App

A Django-based travel application with REST API capabilities.

## Features

- Django REST Framework for API development
- MySQL database configuration
- Swagger API documentation
- CORS headers configuration
- Celery for asynchronous tasks
- RabbitMQ as message broker

## Setup

1. Clone the repository
2. Create virtual environment: `python -m venv venv`
3. Activate virtual environment: `source venv/bin/activate`
4. Install dependencies: `pip install -r requirements.txt`
5. Configure environment variables in `.env` file
6. Run migrations: `python manage.py migrate`
7. Start development server: `python manage.py runserver`

## API Documentation

Access Swagger documentation at: `http://localhost:8000/swagger/`

## Database Setup

Make sure MySQL is running and create the database:
```sql
CREATE DATABASE alx_travel;

Environment Variables
Create a .env file with:

DEBUG

SECRET_KEY

DB_NAME

DB_USER

DB_PASSWORD

DB_HOST

## Final Steps

1. **Set up MySQL database**:
   ```sql
   CREATE DATABASE alx_travel;

Run migrations:
python manage.py migrate

Test the setup:
python manage.py runserver

Then visit http://localhost:8000/swagger/ to see the API documentation.

Start Celery worker (in a new terminal):
celery -A alx_travel_app worker --loglevel=info

The project is now set up with all the required components:

. Django project with listings app

. MySQL database configuration with environment variables

. REST framework and CORS headers

. Swagger documentation at /swagger/

. Celery with RabbitMQ

. Git repository with initial commit

now I can proceed with developing my travel listings functionality
