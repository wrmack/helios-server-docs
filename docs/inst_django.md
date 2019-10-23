# Django installation

### settings.py

Customise the following in settings.py:

    ADMINS
    HOSTS
    DATABASES
    TIME, LANGUAGE
    HELP_EMAIL_ADDRESS
    Authentication systems
    GOOGLE_CLIENT_ID
    GOOGLE_CLIENT_SECRET
    Email settings


### collectstatic

What it does:

* collects all static files that are located in the static folders of the installed apps and stores them in the folder that is set as `STATIC_ROOT` (currently the /static folder).

When to use:

* the `STATIC_ROOT` folder is empty 
* or you have changed the static files in one of the installed app's static folders

How to use:

* to completely refresh the files held in `STATIC_ROOT`, empty it first
* in terminal: 

```
collectstatic
```


### makemigrations

What it does:

* Migrations are Django’s way of propagating changes you make to your models (adding a field, deleting a model, etc.) into your database schema. `makemigrations` creates new migrations based on the changes you have made to your models.

When to use:

* After you make a change to your models.

How to use:

* in a terminal: 
```
python manage.py makemigrations
```

### reset database 
What it does:

* deletes your helios database, creates a new helios database and migrates your models to the database.

When to use:

* After you have run `makemigrations`

How to use:

```
./reset.sh
```

