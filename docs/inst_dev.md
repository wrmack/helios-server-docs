# Local development

## Tasks

* install 'Celery' to run asynchronous tasks. 

* install  RabbitMQ as the message broker

The [celery project](https://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html#choosing-a-broker) recommends `RabbitMQ`.

The following assumes `RabbitMQ` has been installed with binaries in /usr/local/sbin.

## Flow for starting helios 

### Start rabbitmq
In a new terminal:
```
/usr/local/sbin/rabbitmq-server
```
### Start celery
In a new terminal, in helios-server root folder:
```
source venv/bin/activate
celery -A helios worker -l info
```

### Start helios
In settings.py 
```
PRODUCTION = False
```

In a new terminal:
```
source venv/bin/activate
python manage.py runserver
```
### View in browser
In browser go to: `http://localhost:8000`

### Visual Studio Code
If using Visual Studio Code, to launch helios using the debugger, add this to "configurations" in launch.json:
```

    {
        "name": "Python: Django",
        "type": "python",
        "request": "launch",
        "program": "${workspaceFolder}/manage.py",
        "args": [
            "runserver",
            "--noreload"
        ],
        "django": true
    }

```