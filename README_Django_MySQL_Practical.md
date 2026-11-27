# Django + MySQL Integration Practical

A simple Django application demonstrating how to connect Django to an
external MySQL database, define a model, create database tables using
migrations, and insert/retrieve records using Django ORM.

## Practical Details

**Title:** Integrate an external database with Django instead of the
default SQLite database.

**Technologies:** - Python - Django - MySQL - PyMySQL - VS Code - MySQL
Workbench (optional, for database inspection)

## 1. Requirements

Install or make sure you have:

1.  Python 3 installed.
2.  MySQL Server installed and running.
3.  VS Code (or another code editor).
4.  MySQL Workbench is optional.

Check Python:

``` bash
python3 --version
```

Check pip:

``` bash
python3 -m pip --version
```

Make sure your MySQL server is running before continuing.

## 2. Create the MySQL Database

Open MySQL Workbench and connect to your local MySQL server. Run:

``` sql
CREATE DATABASE django_practical;
SHOW DATABASES;
```

The database name must match the `NAME` value in Django's database
configuration.

The example below uses the MySQL account `root`, host `localhost`, and
port `3306`. Use your own MySQL username, password, host, and port if
they are different.

## 3. Create the Project and Virtual Environment

Open a terminal in VS Code and run these commands one at a time:

``` bash
mkdir django_mysql_practical
cd django_mysql_practical

python3 -m venv venv
source venv/bin/activate

python -m pip install --upgrade pip
pip install django pymysql

django-admin startproject config .
python manage.py startapp students
```

When the virtual environment is active, the terminal prompt usually
displays `(venv)`.

Whenever you open a new terminal for this project, activate the
environment again:

``` bash
cd django_mysql_practical
source venv/bin/activate
```

## 4. Configure PyMySQL

Open `config/__init__.py` and add:

``` python
import pymysql

pymysql.install_as_MySQLdb()
```

This lets Django use PyMySQL as the MySQL database driver.

## 5. Register the Students App

Open `config/settings.py`. Add `students` to `INSTALLED_APPS`:

``` python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'students',
]
```

## 6. Configure MySQL in Django

In the same `config/settings.py` file, find the `DATABASES` setting and
replace the default SQLite configuration with:

``` python
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
```

Replace `YOUR_MYSQL_PASSWORD` with your actual MySQL password. Do not
include angle brackets. If your MySQL account has no password, use an
empty string (`''`) only if that is how your local MySQL account is
configured.

**Important:** Keep your database credentials private. Do not publish
real passwords in a public Git repository.

## 7. Create the Student Model

Open `students/models.py` and replace its contents with:

``` python
from django.db import models


class Student(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    course = models.CharField(max_length=100)

    def __str__(self):
        return self.name
```

Django automatically adds an `id` primary-key field because no primary
key was specified.

## 8. Register the Model in Django Admin

Open `students/admin.py` and add:

``` python
from django.contrib import admin
from .models import Student

admin.site.register(Student)
```

This makes the Student model available in the Django Admin interface.

## 9. Configure URLs

The default `config/urls.py` created by Django can remain as it is:

``` python
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

The practical uses Django Admin to add and view Student records, so a
separate application URL or HTML template is not required.

## 10. Create the Database Tables

Make sure the virtual environment is active and the MySQL server is
running.

Run:

``` bash
python manage.py makemigrations students
python manage.py migrate
```

-   `makemigrations` creates migration instructions based on model
    changes.
-   `migrate` applies those instructions to the configured MySQL
    database.

Django creates its built-in tables as well as the `students_student`
table.

## 11. Create a Superuser

Run:

``` bash
python manage.py createsuperuser
```

Enter a username, email address (if requested), and password. The
password will not appear while you type it; this is normal.

A superuser created by Django normally has `is_staff`, `is_superuser`,
and `is_active` set to `True`.

If you already created a superuser, do not create another one unless
needed.

### If the admin says the account must be a staff account

Open the Django shell:

``` bash
python manage.py shell
```

Run the following, replacing `YOUR_USERNAME` with your actual username:

``` python
from django.contrib.auth.models import User

u = User.objects.get(username="YOUR_USERNAME")

print(u.is_superuser, u.is_staff, u.is_active)
```

For an admin superuser, all three values should be `True`. If the
account is yours and these flags are incorrect, you can correct them:

``` python
u.is_staff = True
u.is_superuser = True
u.is_active = True
u.save()
```

If the password is incorrect, exit the shell and reset it with:

``` bash
python manage.py changepassword YOUR_USERNAME
```

Do not share your password with anyone.

## 12. Run the Django Development Server

Run:

``` bash
python manage.py runserver
```

Open the admin page:

<http://127.0.0.1:8000/admin/>

Log in with the superuser username and password.

In Django Admin:

1.  Find the **Students** section.
2.  Select **Add**.
3.  Enter the student's name, email, and course.
4.  Select **Save**.
5.  The saved record should appear in the Students list.

Stop the development server with `Control + C` in the terminal.

## 13. Insert and Retrieve Records Using Django ORM

You can also test the database connection through the Django shell.

Start the shell:

``` bash
python manage.py shell
```

Import the model:

``` python
from students.models import Student
```

Create a record:

``` python
Student.objects.create(
    name="Prajwal",
    email="prajwal@gmail.com",
    course="Computer Engineering"
)
```

Retrieve all records:

``` python
Student.objects.all()
```

Print the fields:

``` python
for student in Student.objects.all():
    print(student.id, student.name, student.email, student.course)
```

Exit the shell:

``` python
exit()
```

Use a different email or remove the previous test record if you repeat
the insert and do not want duplicate test entries.

## 14. Verify the Data in MySQL Workbench

Run:

``` sql
USE django_practical;

SHOW TABLES;

SELECT * FROM students_student;
```

The `students_student` table should contain the records created through
Django Admin or the Django ORM shell.

## 15. Useful Commands

  -------------------------------------------------------------------------------------
  Purpose                             Command
  ----------------------------------- -------------------------------------------------
  Create virtual environment          `python3 -m venv venv`

  Activate environment on macOS       `source venv/bin/activate`

  Install Django and PyMySQL          `pip install django pymysql`

  Create project                      `django-admin startproject config .`

  Create app                          `python manage.py startapp students`

  Create migrations                   `python manage.py makemigrations students`

  Apply migrations                    `python manage.py migrate`

  Create admin user                   `python manage.py createsuperuser`

  Reset user password                 `python manage.py changepassword YOUR_USERNAME`

  Start development server            `python manage.py runserver`

  Open Django shell                   `python manage.py shell`

  Deactivate virtual environment      `deactivate`
  -------------------------------------------------------------------------------------

## 16. Common Errors and Solutions

### Error: Access denied for user

-   Check the MySQL username and password in `config/settings.py`.
-   Confirm that the MySQL account has permission to access the
    database.

### Error: Can't connect to MySQL server

-   Make sure MySQL Server is running.
-   Verify the host and port in the database configuration.

### Error: No module named `pymysql`

Activate the project's virtual environment and run:

``` bash
pip install pymysql
```

### Error: Unknown database

Create the database in MySQL:

``` sql
CREATE DATABASE django_practical;
```

### Error: Table does not exist

Run:

``` bash
python manage.py makemigrations students
python manage.py migrate
```

### Django Admin rejects the login

-   Check that you are using the correct username and password.
-   Confirm that the account has `is_staff=True` and `is_active=True`.
-   Make sure you are running the project with the same settings and
    database where the superuser was created.

## 17. Expected Result

The Django application connects to the external MySQL database named
`django_practical`. The Student model is converted into a MySQL table
through Django migrations. Student records can be added and viewed
through Django Admin or the Django ORM shell, and the records can be
verified in MySQL Workbench.

## 18. Conclusion

The practical demonstrates successful integration of an external MySQL
database with a Django application instead of the default SQLite
database. It covers database configuration, PyMySQL connectivity, Django
models, migrations, ORM operations, and verification of stored records
in MySQL.

------------------------------------------------------------------------

**Note:** This README documents the Django--MySQL integration practical.
It is not the separate Django Calculator experiment shown elsewhere in
the Web Technology Lab Manual.
