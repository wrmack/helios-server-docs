# Local development


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
*(In settings.py* PRODUCTION = False *to tell Django to use the development server)*


In a new terminal:
```
source venv/bin/activate
python manage.py runserver
```
### View in browser
In browser go to: `http://localhost:8000`

### Visual Studio Code
If using Visual Studio Code, to launch helios using the debugger add this to "configurations" in launch.json:
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
        "django": true,
        "justMyCode": false
    }

```

<span style="font-size:10pt">Set "justMyCode" to True for Call Stack to show your own code only.</span>