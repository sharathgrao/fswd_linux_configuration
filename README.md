# Project: Linux Server Configuration
 
URL & IP Address:
```
IP Address: 13.234.34.112
Public URL: http://13.234.34.112.xip.io/
```

PORT: 2200

User: grader

Login Key: 
```
-> ssh -i udacity_key.rsa grader@13.234.34.112 -p 2200
```

Softwares Used:
Openssh Server
Apache2
PostgreSQL
GIT
mod_wsgi
Python
virtualenv
Flask
requests
httplib2
sqlalchemy
psycopg2
oauth2client
render_template
sqlalchemy_utils
redirect


How To:

<b>Amazon Lightsail</b>

1. Create Lightsail account and new Instance
2. Connect using SSH
3. Download private key
4. In the Networking tab, add two new custom ports - 123 and 2200
5. Server configuration
6. Place private key in .ssh
7. Update permission
```
$ chmod 600 ~/.ssh/LightsailDefaultPrivateKey-us-east-2.pem

$ ssh -i udacity_key.rsa ubuntu@13.234.34.112 -p 2200
```
9. Create new account grader
```bash
$ sudo su -
$ sudo nano /etc/sudoers.d/grader Add grader ALL=(ALL:ALL) ALL
$ sudo nano /etc/hosts Under 127.0.1.1:localhost add 127.0.1.1 ip-10-20-37-65
```
10 Install updates and finger package
<code>$ sudo apt-get update $ sudo apt-get upgrade $ sudo apt-get install finger</code>
Keygen
In a new terminal, `$ ssh-keygen -f ~/.ssh/udacity_key.rsa
```$ cat ~/.ssh/udacity_key.rsa.pub
```
In the original terminal, $ cd /home/grader
```$ mkdir .ssh
$ touch .ssh/authorized_keys
$ nano .ssh/authorized_keys
```
Permissions: 
```$ sudo chmod 700 /home/grader/.ssh $ sudo chmod 644 /home/grader/.ssh/authorized_keys
$ sudo chown -R grader:grader /home/grader/.ssh
$ sudo service ssh restart
```
NOW Disconnect: ```$ ~.```

RECONNECT WITH GRADER
```$ ssh -i ~/.ssh/udacity_key.rsa grader@13.234.34.112```
Enforce key based authentication
```$ sudo nano /etc/ssh/sshd_config```
Find the PasswordAuthentication line and change text after to no
```$ sudo service ssh restart```
Change port
```$ sudo nano /etc/ssh/sshd_config```
Find the Port line and change 22 to 2200
```$ sudo service ssh restart
$ ~.
$ ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@13.234.34.112
```

Disable root login
```
$ sudo nano /etc/ssh/sshd_config
```

Find the PermitRootLogin line and edit to no
```
$ sudo service ssh restart
```

Configure UFW
```
$ sudo ufw allow 2200/tcp $ sudo ufw allow 80/tcp $ sudo ufw allow 123/udp $ sudo ufw enable
```

Install Apache and GIT
```
$ sudo apt-get install apache2 $ sudo apt-get install libapache2-mod-wsgi python-dev $ sudo apt-get install git
```

Enable mod_wsgi
```
$ sudo a2enmod wsgi $ sudo service apache2 start
```

Setup Folders
```
$ cd /var/www $ sudo mkdir catalog $ sudo chown -R grader:grader catalog $ cd catalog
```

Clone Catalog Project
```
$ git clone https://github.com/sharathgrao/fswd_itemcatalog.git catalog
```

Create .wsgi file
```
$sudo nano catalog.wsgi
```
```python
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/")

    from catalog import app as application
    application.secret_key = 'super_secret_key'
```

Rename the project.py to __init__.py

Virtual Machine
```
$ sudo pip install virtualenv $ sudo virtualenv venv $ source venv/bin/activate $ sudo chmod -R 777 venv
```

Install flask and other packages
```
$ sudo apt-get -H install python-pip
$ sudo pip -h install Flask
$ sudo pip -h install Requests
$ sudo pip -h install httplib2
$ sudo pip -h install sqlalchemy
$ sudo pip -h install psycopg2
$ sudo pip -h install oauth2client
$ sudo pip -h install render_template
$ sudo pip -h install sqlalchemy_utils
$ sudo pip -h install redirect
$nano __init__.py
```

Change the client_secrets.json line to /var/www/catalog/catalog/client_secrets.json

Update oauth_flow = flow_from_clientsecrets('client_secrets.json', scope='') to oauth_flow = flow_from_clientsecrets('/var/www/catalog/catalog/client_secrets.json', scope='')

Restart Apache.

Change the host to 13.234.34.112 and port to 80
Configure virtual host
```
$ sudo nano /etc/apache2/sites-available/catalog.conf
```
ADD BELOW INFO into Virtual HOST FILE

```
    <VirtualHost *:80>
    ServerName 13.234.34.112
    ServerAdmin admin@13.234.34.112
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
```

Database

```
$ sudo apt-get install libpq-dev python-dev
$ sudo apt-get install postgresql postgresql-contrib
$ sudo su - postgres
$ psql
$ CREATE USER catalog WITH PASSWORD 'password'; $ ALTER USER catalog CREATEDB; 
$ CREATE DATABASE catalog WITH OWNER catalog;
```

Connect to database 
```$ \c catalog```

Revoke the default permissions
```
$ REVOKE ALL ON SCHEMA public FROM public; 

$ GRANT ALL ON SCHEMA public TO catalog;
```

Quit the postgres command line: 
```
$ \q and then $ exit
```

Update the local databse engine from sqlite to postfresql
```
$ nano __init__.py 
```
Edit database_setup.py, and menus.py files to change the database engine from sqlite://catalog.db to postgresql://catalog:password@localhost/catalog

Add http://13.234.34.112.xip.io to Authorized JavaScript Origins and Authorised redirect URIs on Google Developer Console.

Restart Apache2 for the configuration to take effect.

```
$ sudo service apache2 restart
```


WHOLA! Your server is now up and your flask app has now started to run. Visit the URL -> http://13.234.34.112.xip.io/ to see the site. Login to perform CRUD operations.

