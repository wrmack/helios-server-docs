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

* collects all static files that are located in the static of folders of the installed apps and stores them in `STATIC_ROOT`.

When to use:

* `STATIC_ROOT` is empty 
* you have changed the static files in one of the installed app's static folders

How to use:

* to completely refresh the files held in `STATIC_ROOT`, empty it first
* in terminal: 

```
collectstatic
```


### makemigrations

What it does:

When to use:

How to use:

* in terminal: 
```
python manage.py makemigrations
```

### reset database 
What it does:

When to use:

How to use:

```
./reset.sh
```

