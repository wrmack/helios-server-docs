# Environment for local development

If not already installed:

* install [PostgreSQL](https://www.postgresql.org/download/)

* install [Python 3](https://www.python.org/downloads/)

* install [pip](https://packaging.python.org/tutorials/installing-packages/), the python package manager

* install [virtualenv](http://www.virtualenv.org/en/latest/)

then:

* download helios-server (from the branch you want to use - usually the master branch)

* cd into the helios-server directory

* create a virtualenv:

```
virtualenv venv
```

* activate virtual environment

```
source venv/bin/activate
```
* check venv/lib/ to ensure it shows python 3.x is installed in your virtual environment

* install requirements

```
pip install -r requirements.txt
```

