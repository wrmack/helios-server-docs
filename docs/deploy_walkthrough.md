Deploying Helios to Heroku.

## Introduction

This walk-through uses Heroku's free tier and uses free versions of add-ons for the database, process worker and email.


It assumes:

- helios server is installed locally
- helios server is managed through git
- helios server has a remote repository on Github
- you have created an account on Heroku and are logged in
- you have downloaded and installed [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) which we will use for accessing the database.

## Create a web app to run the helios server on Heroku by using Heroku dashboard

- go to [https://dashboard.heroku.com](https://dashboard.heroku.com)
- go to New - **Create new app**
- give it a name and click **Create app**
- you should now be in the "Deploy" tab
- for "Deployment method" select **Github**
- for "Connect to Github" enter your repository name eg helios-server
- when Heroku has found your Github repository it will list it with a Connect button alongside
- click **Connect**
- your app name is referred to below as **APPNAME** - substitute the actual name you gave it

## Preparing helios server for deployment to Heroku

- Heroku requires a Procfile - create a file `Procfile` with the following content
```shell
web: gunicorn wsgi:application -b 0.0.0.0:$PORT -w 8
worker: celery worker --app helios --events --beat --concurrency 1
```
- you may go to ["Deploy by using the Heroku dashboard"](#deploy-by-using-the-heroku-dashboard) below at any time to examine error messages and comfirm whether you need to do the following.

**Secret environment variables** (such as passwords)

- secrets need to be passed to Heroku as environment variables when you set up your Heroku app (so they are not visible in any files you have on Github)
- if you added a `.env` file and imported `environ` into `settings.py` for local development, remove them for deployment to Heroku
- go to the **Settings** tab on Heroku's dashboard page for your app and select **Reveal Config Vars**
- add your authentication credentials, eg in `settings.py` you might have:
          ```shell
          GOOGLE_CLIENT_ID = os.environ['GOOGLEID']
          GOOGLE_CLIENT_SECRET = os.environ['GOOGLESECRET']
          ```
    in which case you would add the keys GOOGLEID and GOOGLESECRET as 'config vars' and enter the appropriate values for each.
    
    Make sure that in [Google console](https://console.developers.google.com/apis/credentials) you enter the correct uris: 
    ```shell
    # Authorised JavaScript origins

    https://APPNAME.herokuapp.com

    #Authorised redirect URIs

    https://APPNAME.herokuapp.com/auth/after/
    ```


**Settings**

- other changes in `settings.py`:
    - add the Heroku host to the ALLOWED_HOSTS array
    ```shell
    ALLOWED_HOSTS = ['APPNAME.herokuapp.com']
    ```
    - for URL_HOST add the Heroku url (it will automatically be used for the SECURE_URL_HOST)
    ```shell
    URL_HOST = get_from_env("URL_HOST", "https://APPNAME.herokuapp.com").rstrip("/")
    ```
    - database settings should be:
    ```shell
    DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.postgresql_psycopg2',
          'NAME': 'helios',
          'CONN_MAX_AGE': 600,
      },
    }

    # override if we have an env variable
    if get_from_env('DATABASE_URL', None):
      import dj_database_url
      DATABASES['default'] = dj_database_url.config(conn_max_age=600, ssl_require=True)
      DATABASES['default']['ENGINE'] = 'django.db.backends.postgresql_psycopg2'
    ```


**Celery**

- Celery requires a process worker - we will use CloudAMQP which is available as a Heroku add-on
- on the Heroku dashboard page for your app go to the **Resources** tab and select **Find more add-ons**
- under **Add-on categories** go to **Messaging and Queuing**
- select **CloudAMQP**
- under **Plans & Pricing** select **Little Lemur** (the free option) and **Install CloudAMQP**
- to provision the add-on for your app enter the name of your app and click **Submit Order Form**
- check it has been added by going to the **Settings** page  - Config Vars should now include a key CLOUDAMQP_URL
- on the **Resources** page switch on celery worker if it is not already
- in `settings.py` add:
```shell
CELERY_BROKER_URL = os.environ['CLOUDAMQP_URL']
BROKER_URL = os.environ['CLOUDAMQP_URL']
```

**Dependencies**

- Heroku loads all dependencies that are in `requirements.txt`
- if, in your local version of Helios, you loaded any dependency separately to what is in `requirements.txt` Heroku will not know about it, so put it in `requirements.txt`.

**Collectstatic**

- by default, Heroku runs `python manage.py collectstatic` which we don't need so disable it:
```shell
heroku config:set DISABLE_COLLECTSTATIC=1 --app APPNAME
```

## Deploy by using the Heroku dashboard

- commit all your changes and push to Github
- in Heroku dashboard for your app, in the **Deploy** tab, if you haven't set **Enable Automatic Deploys**, next to **Manual deploy** select the branch you want to deploy and click **Deploy branch**
- Heroku will attempt to install your helios server web app and it displays a log as it does this
- if it installs successfully Heroku will launch it
- click **Open app** at the top of the page to view your web page
- if launching fails, examine logs in a terminal with
```shell
heroku logs --tail --app APPNAME
```
- there will be an error message because we have not set up the Postgreslq database yet.

**Database**

- Heroku should have attached a Postgresql database to your app and created the 'helios' database in line with your database settings in `settings.py`
- create a Heroku shell and populate the 'helios' database
```shell
heroku run bash --app APPNAME
~ $ python manage.py makemigrations
~ $ python manage.py migrate
~ $ exit
```
- check database tables have been created:
```shell
heroku pg:psql --app APPNAME
APPNAME::DATABASE=> \d
APPNAME::DATABASE=> \q
```
- (optional) open your app again, or refresh it if it is already open in your browser - it should run this time, but you should be able to create an election but cannot yet send emails.

## Email

- we need to set up an add-on that will take care of sending emails to voters - Heroku has a couple of third party add-ons that do this
- on the Heroku dashboard page for your app go to the **Resources** tab and select **Find more add-ons**
- under **Add-on categories** go to **Email/SMS**
- select **Mailgun** (or Sendgrid, Trustifi, CloudMailin)
- under **Plans & Pricing** select **Starter** (the free option) and **Install Mailgun**
- to provision the add-on for your app enter the name of your app and click **Submit Order Form**
- check it has been added by going to the **Settings** page  - Config Vars should now include a number of keys relating to Mailgun
- add the following to settings.py
```shell
EMAIL_HOST = os.environ['MAILGUN_SMTP_SERVER']
EMAIL_HOST_USER = os.environ['MAILGUN_SMTP_LOGIN']
EMAIL_HOST_PASSWORD = os.environ['MAILGUN_SMTP_PASSWORD']
EMAIL_PORT = 587
EMAIL_USE_TLS = True
```
- Mailgun places starting users in sandbox accounts which require email receipients to verify their email addresses (you can send an email to yourself without verification)
- to add recipients, on the Heroku dashboard page for your app go to the **Resources** tab and click on the Mailgun add-on 
- on the Mailgun dashbord, select the sandbox account
- on the right there is a side-box where it is possible to add recipients
- after a recipient is added, a confirmation email is automatically sent to the recipient who has to click an "I agree" button
- following the changes to settings.py, commit and push to Github
- go to the Heroku dashboard and, under Deploy, do a manual deploy if you have not set it up for automatic deploy
- you should now be able to send emails to yourself and verified recipients from within Helios


