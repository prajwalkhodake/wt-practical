# wt-practical
https://docs.google.com/document/d/1hqmfjb-aFP54-gDdFz0rr0DanWQ9lh1CcoP79zs6ov0/edit?usp=sharing

mkdir django_mysql_practical
cd django_mysql_practical

python3 -m venv venv
source venv/bin/activate

pip install django pymysql

django-admin startproject config .
python manage.py startapp students

Configure MySQL in Django
First, open this file:

config/__init__.py
import pymysql

pymysql.install_as_MySQLdb()

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'students',
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django_practical',
        'USER': 'root',
        'PASSWORD': 'YOUR_MYSQL_PASSWORD',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}


from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    course = models.CharField(max_length=100)

    def __str__(self):
        return self.name


python manage.py makemigrations

python manage.py migrate

python manage.py shell

from students.models import Student

Student.objects.create(
    name="Prajwal",
    email="prajwal@gmail.com",
    course="Computer Engineering"
)
Student.objects.all()

for student in Student.objects.all():
    print(student.name, student.email, student.course)

exit()

Verify in MySQL Workbench

USE django_practical;

SHOW TABLES;

SELECT * FROM students_student;

python manage.py runserver

python manage.py createsuperuser
python manage.py changepassword YOUR_USERNAME
