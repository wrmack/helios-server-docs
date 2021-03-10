Deploying Helios to AWS (Amazon Web Services)

## Introduction

AWS provides a large range of services.  This deployment uses the following services:

- Elastic Compute, or EC2, which provides a virtual server instance
- Elastic Block Store, or EBS, which provides the 'hard drive' for the server instance
- Elastic IP Address, which provides a persistent IP address (the IP address associated with an EC2 instance may change if the instance is stopped and restarted)
- Route 53 for obtaining a domain name (required for setting up https)
- Simple Email Service, or SES, for sending emails 
- Identity and Access Management, or IAM, for obtaining keys for programmatic access to the SES API

All of these services are in the free tier except for a domain name which costs $12 USD per year for a .com name.  The free tier includes services which are always free and services which are only free for the first 12 months.  The most expensive service of the above is the EC2 instance which is free for 12 months.  Once outside the free tier's 12 month period if you are paying on demand you might consider stopping the instance when you are not using it (eg if you are only using it for development purposes).  Data persists provided the EBS remains attached.

Other options might include:

- Lightsail, instead of EC2, which provides an easier setup process and has an option for Django framworks
- Relational Database Service, or RDS, for providing a postgresql database (our deployment will simply install postgresql on the EC2 instance)

This deployment assumes:

- you have created an account on AWS and are logged into the management console
- you have set up authentication for Google OAuth (to allow logging in to Helios)

Caveat: this deployment has not been tested with a real election.  It may help to get you started.

## Create an EC2 instance

- click on **Services** in the top menu bar and from the drop-down select **EC2** under **Compute**
- click on the red button **Launch instance**
- select the free tier **Amazon Linux 2** AMI (should be the very first one)
- for instance type select the free tier **t2.micro** then click the button **Configure Instance**
- no changes, click button **Add Storage**; this is the EBS volume to be attached to your EC2 instance with a default size of 8 GB
- no changes, click button **Add Tags**; adding tags is optional
- click button **Configure Security Group** which configures which ports should be open
- add these rules to the default SSH one:
    - Postgresql (from drop-down) (port 5432, source 0.0.0.0/0)
    - HTTP
    - HTTPS
- click button **Review and Launch**
- click button **Launch** which pops up a window for creating a key pair
- create a new key pair, give it a name and download the private key (.pem file), then click **Launch**
- click on **View Instances** to go to the EC2 Instances page
- click on the Instance ID to view detailed information about the instance
- get an Elastic IP which will persist: 
    - in left side-bar click Elastic IPs
    - click red button **Allocate Elastic IP address**
    - click red button **Allocate**
    - on Elastic IP addresses page select the Elastic IP address then in the **Actions** drop-down select **Associate Elastic IP address**
    - in the **Instance** box select your instance then click the red button **Associate**
- you now have remote SSH access to your instance using its **Elastic IP address** or **Public IPv4 DNS**
- EC2 users are all `ec2-user`
```shell
ssh -i path_to_keypair.pem ec2-user@elastic_ip_address
```
- the following shows the virtual disk space on your EBS volume:
```shell
sudo df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      8.0G  1.4G  6.7G  18% /
```


## Upload Helios 

- download master branch of Helios to a local directory 
- use rsync to upload to your EC2 instance (substitute correct values for path_to_keypair.pem, path_to/helios-server-master, elastic_ip_address):
```shell
rsync -av -e "ssh -i path_to_keypair.pem" path_to/helios-server-master  ec2-user@elastic_ip_address:/home/ec2-user
```

## Install python, postgresql, helios requirements, rabbitmq 

- do the following:
```
# ssh into your instance (if not already in it) 
ssh -i path_to_keypair.pem ec2-user@elastic_ip_address

# python3
sudo yum install python3 -y

# postgresql
sudo yum install postgresql-server -y
sudo postgresql-setup initdb
sudo systemctl enable postgresql.service
sudo systemctl start postgresql.service
sudo -i -u postgres
psql postgres
CREATE USER "ec2-user" WITH SUPERUSER;
CREATE DATABASE helios WITH OWNER "ec2-user";
\q
exit

# rabbitmq
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash
sudo yum install erlang -y
sudo yum install rabbitmq-server -y
sudo /sbin/service rabbitmq-server start

# requirements.txt
sudo yum install gcc -y               # Required to build psycopg2 from source
sudo yum install python3-devel -y     # Required to build psycopg2 from source
sudo yum install postgresql-devel -y  # Required to build psycopg2 from source
cd ~/helios-server-master
python3 -m venv venv
source venv/bin/activate
python3 -m pip install wheel
python3 -m pip install -r requirements.txt
python3 -m pip install django-environ  # So can use environ module
python3 manage.py migrate              # Initialise the helios database


# start celery (still in venv environment)
celery -A helios worker -l INFO
```

## Nginx

- if you wish to leave celery running then open another termimal and 
```
ssh -i path_to_keypair.pem ec2-user@elastic_ip_address
```
- install Nginx
```shell
sudo amazon-linux-extras install nginx1 -y
```
- create /etc/systemd/system/gunicorn.socket
```
sudo nano /etc/systemd/system/gunicorn.socket
```
- content of gunicorn.socket
```
[Unit]      
Description=gunicorn socket

[Socket]      
ListenStream=/run/gunicorn.sock

[Install]      
WantedBy=sockets.target
```
- create /etc/systemd/system/gunicorn.service
```
sudo nano /etc/systemd/system/gunicorn.service
```

- content of gunicorn.service
```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=ec2-user
Group=ec2-user
WorkingDirectory=/home/ec2-user/helios-server-master
ExecStart=/home/ec2-user/helios-server-master/venv/bin/gunicorn \
--access-logfile - \
--workers 3 \
--bind unix:/run/gunicorn.sock \
wsgi:application

[Install]
WantedBy=multi-user.target
```
- open /etc/nginx/nginx.conf
```
sudo nano /etc/nginx/nginx.conf
```

- content of nginx.conf 
```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    include            /etc/nginx/conf.d/*.conf;
    include            /etc/nginx/sites-enabled/*;   

    server {
        listen         80;
        listen         [::]:80;
        server_name    _;
        root           /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include        /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
```
- create directory /etc/nginx/sites-available
```
sudo mkdir /etc/nginx/sites-available
```
- create /etc/nginx/sites-available/helios
```
sudo nano /etc/nginx/sites-available/helios
```
- content of helios (replace your_elastic_ip with your own)
```
server {
    listen        80;
    server_name   your_elastic_ip;

    location =    /favicon.ico { access_log off; log_not_found off; }

    location / {
      proxy_pass http://unix:/run/gunicorn.sock;
    }
}
```
- link sites-enabled to sites-available
```
sudo ln -s /etc/nginx/sites-available /etc/nginx/sites-enabled
```

## Update settings.py

- these keys should have these values (replace your_elastic_ip with your own)
```
ALLOWED_HOSTS = ['your_elastic_ip','localhost']

CELERY_BROKER_URL = get_from_env('CELERY_BROKER_URL', 'amqp://localhost')
```

## Check if site loads - 1

- start services
```
sudo systemctl start gunicorn.socket
sudo systemctl enable gunicorn.socket
sudo systemctl daemon-reload
sudo systemctl start gunicorn.service
sudo systemctl start nginx
```
- in your browser go to http://your_elastic_ip_address
- it will not be possible to log in because we have not yet set up Google authentication which requires SSL
- options for troubleshooting and viewing error messages:
```
sudo nginx -t  # Checks configuration of Nginx
sudo systemctl status nginx.service
sudo cat /var/log/nginx/error.log
sudo cat /var/log/nginx/access.log
sudo journalctl -xe
```

## Convert site to https

- we use certbot which blacklists AWS instance names (because they are ephemeral) so we need a permanent domain name
- there is a [cost of $12 USD](https://console.aws.amazon.com/route53/home#DomainRegistration:example.com) per year for a .com name
- obtain a domain name by going to the Route 53 service
    - click the **Services** drop-down on the AWS menubar 
    - select **Route 53** under **Networking and Content Delivery**
    - in the left side-bar select **Registered domains** then click the blue button **Register Domain**
    - follow the prompts to purchase a domain name
    - back on the Route 53 home page select **Hosted zones** in the left side-bar
    - under **Quick create record** create a subdomain name (eg helios to give helios.your_domain.com)
    - record type should be **A** and **Value type** should be your elastic ip
    - click the red button **Create records**
    - you have now associated your sub-domain with your elastic ip address
- update /etc/nginx/sites-available/helios to automatically redirect http requests to https and add a server block for https (replace your_domain_name with your own one - there are four):
```shell
server {
  listen        80;
  server_name   your_domain_name;
  return        301 https://$host$request_uri;    # Causes http to redirect to https
}

server {
   listen       443 ssl http2;
   listen       [::]:443 ssl http2;
   server_name  your_domain_name;

   ssl_certificate "/etc/letsencrypt/live/your_domain_name/cert.pem";
   ssl_certificate_key "/etc/letsencrypt/live/your_domain_name/privkey.pem";
   ssl_session_cache shared:SSL:1m;
   ssl_session_timeout  10m;
   ssl_ciphers HIGH:SEED:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!RSAPSK:!aDH:!aECDH:!EDH-DSS-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA:!SRP;
   ssl_prefer_server_ciphers on;

   # Load configuration files for the default server block.
   include /etc/nginx/default.d/*.conf;

   error_page 404 /404.html;
       location = /40x.html {
   }

   error_page 500 502 503 504 /50x.html;
       location = /50x.html {
   }
}
```
- install [certbot](https://certbot.eff.org/lets-encrypt/otherpip-nginx)
```
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
sudo /opt/certbot/bin/pip install certbot certbot-nginx
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
sudo certbot --nginx

# Automatic renewal - optional
echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && certbot renew -q" | sudo tee -a /etc/crontab > /dev/null
```
- that should automatically check for correct configuration, if it fails for any reason you may need to manually put the certificate and key in the correct directory
- you should have a .env file in your helios root directory for keeping secret credentials:
```
GOOGLESECRET=xxxxxxxxxxxxxxxxxx
GOOGLEID=xxxxxxxxxxxxxxxxxx
```
- update settings.py - these seem to be the key changes to make:
```
# Read in secrets from your .env file
import environ
env = environ.Env()
environ.Env.read_env()
GOOGLESECRET = env('GOOGLESECRET')
GOOGLEID = env('GOOGLEID')

DEBUG = (get_from_env('DEBUG', '0') == '0')

ALLOWED_HOSTS = ['localhost',''your_elastic_ip','your_domain_name']

URL_HOST = get_from_env("URL_HOST", "https://your_domain_name).rstrip("/")

GOOGLE_CLIENT_ID = get_from_env('GOOGLE_CLIENT_ID', GOOGLEID)
GOOGLE_CLIENT_SECRET = get_from_env('GOOGLE_CLIENT_SECRET', GOOGLESECRET)

```
- if you use Google for authentication you will need to add your new domain name to authorised urls in Google [credentials](https://console.developers.google.com/apis/credentials)

## Check if site loads - 2

- start celery in venv environment if not already running
```
celery -A helios worker -l INFO
```
- restart all services
```
sudo systemctl restart gunicorn.socket
sudo systemctl daemon-reload
sudo systemctl restart gunicorn.service
sudo systemctl restart nginx
```
- in your browser go to https://your_domain_name
- if that loads without error check that http redirects to https:  http://your_domain_name
- try logging on with Google
- if all this succeeds you should be able to set up an election but not be able to email voters yet

## Set up email

- AWS provides the AWS Simple Email Serivce (SES)
- there is a django backend ([django-SES](https://github.com/django-ses/django-ses)) that talks to this service
- to use this, it is necessary to have programmatic access to AWS SES APIs
- to create a user who has access, go to the AWS service **IAM** (in the drop-down list of services under **Security, Identity & Compliance**) 
- in the left side-bar select **Users** then click the blue button **Add user**
- provide a user name and select **Programmatic access**
- select **Attach existing policies directly** then search for **SES** and select **AmazonSESFullAccess**
- (optional) add a tag
- review and click blue button **Create user**
- important: record the **Access key ID** and **Secret access key**
- in your .env file add 
```
AWS_SES_ACCESS_KEY_ID=your_access_key_id
AWS_SES_SECRET_ACCESS_KEY=your_secret_access_key
```
- in settings.py include the following entries:
```
AWS_SES_REGION_NAME = 'us-east-1'                              # change this if you use a different region
AWS_SES_REGION_ENDPOINT = 'email.us-east-1.amazonaws.com'      # change this if you use a different region
AWS_SES_ACCESS_KEY_ID = env('AWS_SES_ACCESS_KEY_ID')
AWS_SES_SECRET_ACCESS_KEY = env('AWS_SES_SECRET_ACCESS_KEY')

EMAIL_BACKEND = 'django_ses.SESBackend'
```
- AWS SES initially places your SES account in a sandbox (it has restrictions) and requires email senders to be verified
- on the Amazon SES page, in the left side-bar, select **Verified identities** and add email addresses for verification
- the system sends an email to each address asking for verification by reply
- make sure **DEFAULT_FROM_EMAIL** in settings.py is a verified email address
- optionally, in your venv environment, test email works with:
```
python manage.py shell
>>> from django.core.mail import send_mail
>>> send_mail('Subject', 'Message', 'one_of_your_verified_email_addresses_as_sender', ['one_of_your_verified_email_addresses_as_receiver'])
```
## Check if site loads - 3

- start celery in venv environment if not already running
```
celery -A helios worker -l INFO
```
- restart all services
```
sudo systemctl restart gunicorn.socket
sudo systemctl daemon-reload
sudo systemctl restart gunicorn.service
sudo systemctl restart nginx
```
- in your browser go to https://your_domain_name 