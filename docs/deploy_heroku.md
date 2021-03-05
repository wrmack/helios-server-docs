# Heroku

**About Heroku**

Heroku is a subsidiary of salesforce.com. Its servers are hosted on Amazon's EC2 cloud-computing platform.

When an app is deployed it runs in a container called a ‘dyno’.  There is no direct access to its filesystem.  There is more about dynos [here](https://www.heroku.com/dynos).

Heroku’s pricing includes a [free plan](https://www.heroku.com/pricing ) which is suitable for experimental purposes. It provides for 1 web process and 1 worker process (Celery in our case).  The Heroku Postgres database is free within a limit of 10K rows.  There is more about the free dyno [here](https://devcenter.heroku.com/articles/free-dyno-hours).

**Deployment**

There are options for deploying an app.  One option is to link to a Github repository so that a push to Github triggers a build and deployment.

**Configuration**

There is one required configuration file - Procfile.  This tells Heroku what to run when an app is deployed.  I use the following for running one web server process and one Celery worker process:

```
web: gunicorn wsgi
worker: celery worker -A helios -l info
```

**Building and debugging**

Build logs can be viewed through Heroku’s web interface, but for anything more than that it is important to become familiar with the Heroku CLI application.  Heroku CLI can also run a psql shell for examining the database. Run this for a real-time display of logs generated on the Heroku site (if helios-heroku is the name of your app on Heroku):

```
heroku logs --tail -a helios-heroku
```

 **Helios requirements**

For tasks, Celery may be used and there are options for a broker (for example Redis).

For sending email, an add-on, [SendGrid](https://elements.heroku.com/addons/sendgrid), can be used. This is free up to 12,000 emails per month. 

## Demo

**Code**

My `wm-py3-django2-heroku` branch:

[https://github.com/wrmack/helios-server/tree/wm-py3-django2-heroku](https://github.com/wrmack/helios-server/tree/wm-py3-django2-heroku)

**Live**

[https://helios-heroku.herokuapp.com](https://helios-heroku.herokuapp.com)