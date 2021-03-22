# Environment essentials for local development

If not already installed:

* install [Python 3](https://www.python.org/downloads/)

* install [PostgreSQL](https://www.postgresql.org/download/)

* install [Celery](https://docs.celeryproject.org/en/latest/django/index.html) to run asynchronous tasks 

* install [RabbitMQ](https://www.rabbitmq.com) as the message broker [recommended by Celery](https://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html#choosing-a-broker)

* install [pip](https://packaging.python.org/tutorials/installing-packages/), the python package manager


then:

* download helios-server 

* cd into the helios-server directory

* create a virtual environment:

```
python3 -m venv venv  
```

* activate the virtual environment:

```
source venv/bin/activate
```


* install dependency requirements:

```
python3 -m pip install -r requirements.txt
```


