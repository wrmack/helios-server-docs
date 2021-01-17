# Walk-through of installing Helios on Ubuntu 20.4 

- install VSCode (optional)
- check python3 is installed with Ubuntu and, if not, install it
    - `python3 --version`
- install pip3
    - `sudo apt install python3-pip`
- install python3-venv `sudo apt install python3-venv`
- install Postgresql
    - `sudo apt-get update`
    - `sudo apt install postgresql`
    - `sudo systemctl start postgresql@12-main` (prompted by the installation) 
    - `sudo -i -u postgres` (become the postgres user)
    - `psql postgres` (connect to the postgres database)
    - `CREATE USER [your login name] WITH SUPERUSER;` (create Helios database user)
    - `CREATE DATABASE helios WITH OWNER [your login name];`  (create Helios database)
    - `exit` (logout as the postgres user)
    - `psql helios` (connect to the new helios database)
    - `\l` (list databases - check helios is there)
    - `\q`
- install RabbitMQ
    - `sudo apt install rabbitmq-server`
- setup Google OAuth authentication
    - in a browser go to: https://console.developers.google.com/apis  (you should have a Google account)
    - select `Credentials`
    - `Create a project` and give it a name (eg Helios) then `CREATE CREDENTIALS`
    - `OAuth client ID`
    - `CONFIGURE CONSENT SCREEN` - select `External` then `CREATE`
    - Just complete the required fields (App name, User support email and Developer contact email) then `SAVE AND CONTINUE`
    - Under `Scopes` and `Optional info` add nothing just `SAVE AND CONTINUE` then on Summary page `BACK TO DASHBOARD`
    - select `Credentials` then `CREATE CREDENTIALS`
    - Application type is `Web application`
    - Name is your choice (eg Helios)
    - Authorised Javascript origins: `http://localhost:8000`
    - Authorised redirect URIs: `http://localhost:8000/auth/after/`
    - `CREATE`
    - copy `Client ID` and `Client secret` for using in Helios
    - Select `Dashboard` then `ENABLE APIS AND SERVICES`
    - Select `Google People API` and `ENABLE`

- download Helios code: in a browser 
    - go to https://github.com/wrmack/helios-server
    - change `master` to `wm-python3-django2`
    - under `Code` choose `Download ZIP`
    - once downloaded double-click the Zip arhive to unarchive it and move all to a project folder of your choice for working on (ie move code out of the Downloads folder)
- prepare helios
    - cd to your project folder
    - create a virtual environment `python -m venv venv`
    - activate the virtual environment: `source venv/bin/activate`
    - install all dependencies in requirements.txt: 
        - `pip3 install wheel`  (in response to an error message)
        - `sudo apt install libpq-dev` (required for installation of psycopg2)
        - (in `requirements.txt` I upgraded celery to 5.0.0 and replaced pycrypto with pycryptodome for compatability with python 3.8)
        - `pip3 install -r requirements.txt`
    - create an `.env` file with stored secrets
        ```shell
            DBPWD=xxxx
            GOOGLESECRET=xxxx
            GOOGLEID=xxxx
        ```
- in a separate terminal start celery
    - cd to your helios project
    - `source venv/bin/activate`
    - `celery -A helios worker -l INFO` 
- back at first terminal, run helios
    - `python3 manage.py makemigrations`
    - `./reset.sh`
    - `python3 manage.py runserver`
    - in a browser go to `127.0.0.1:8000`




    