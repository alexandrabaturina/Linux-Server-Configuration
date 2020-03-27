# Linux Server Configuration

**Linux Server Configuration** project consists of the following steps.
* Initial configuring of *Ubuntu* Linux server instance on *Amazon Lightsail*
* Configuring *Apache* web server
* Configuring *PostgreSQL* database server
* Deploying  [```Book Catalog```](https://github.com/alexandrabaturina/Book-Catalog) application

Verify the deployment by visiting http://54.191.192.22.xip.io. 
## Getting Started
To review the project, the ```grader``` user with ```sudo``` permission was created. To log in to the server under ```grader``` user via port ```2200```, use the following command.
```sh
$ ssh grader@54.191.192.22 -p 2200 -i ~/.ssh/linuxServerUdacity
```

## Securing Server
To secure the server, the following steps were taken.
* All currently installed packages are updated using ```sudo apt-get update``` and ```sudo apt-get upgrade``` commands
* The *Lightsail* firewall is configured to allow incoming connections for SSH (port ```2200```)
*  The *Uncomplicated Firewall* (UFW) is configured to allow connections according to project specifications 
### Configuring Uncomplicated Firewall
To host ```SSH``` on a non-default port, ```port 22``` is changed to ```port 2200``` in configuration file ```/etc/ssh/sshd_config```.
```sh
# What ports, IPs and protocols we listen for
Port 2200
```

*UFW* is configured to only allow connections for ```SSH``` (port 2200), ```HTTP``` (port 80), and ```NTP``` (port 123). Below is the list of current UFW rules.
```sh
Status: active
Default: deny (incoming), allow (outgoing), disabled (routed)

To                         Action      From
--                         ------      ----
22                         DENY IN     Anywhere
2200                       ALLOW IN    Anywhere
80                         ALLOW IN    Anywhere
123                        ALLOW IN    Anywhere
123/udp                    ALLOW IN    Anywhere
22 (v6)                    DENY IN     Anywhere (v6)
2200 (v6)                  ALLOW IN    Anywhere (v6)
80 (v6)                    ALLOW IN    Anywhere (v6)
123 (v6)                   ALLOW IN    Anywhere (v6)
123/udp (v6)               ALLOW IN    Anywhere (v6)
```
## Configuring Server
### Configuring Timezone
The local timezone for ```grader``` is configured to UTC using the following command.
```sh
$ sudo timedatectl set-timezone UTC
```
### Installing and Configuring Apache
*Apache* web server is installed using the following command.
```sh
$ sudo apt-get install apache2
```
The ```Book Catalog``` project is built with Python 3, so the Python 3 ```mod_wsgi``` package is installed on the server to host ```Book Catalog``` as a mod_wsgi application.
```sh
$ sudo apt-get install libapache2-mod-wsgi-py3
```
### Installing and Configuring PostgreSQL
*PostgreSQL* database server is installed using the following command.
```sh
$ sudo apt-get install postgresql
```
To disable remote connections to the *PostgreSQL* database, in *PostgreSQL* client authentication configuration file  ```/etc/postgresql/9.5/main/pg_hba.conf``` listen addresses are set to ```127.0.0.1```.
```sh
# Database administrative login by Unix domain socket
local   all             postgres                                peer

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
```
#### Creating ```catalog``` user
Database user account named```catalog``` with limited permissions to ```Book Catalog``` database was created via interactive terminal for working with *PostgreSQL* using the following commands.
```sh
postgres=# create user catalog with login password 'catalog'; 
CREATE ROLE 
postgres=# alter user catalog with createdb; 
ALTER ROLE 
```
## Deploying Project
### Creating Flask Application Structure
To allow *Apache* to serve the ```Book Catalog``` project as a ```WSGI``` applicaton, the following folder structure is used.
```sh
bookCatalog/
    bookcatalog.wsgi
    bookCatalog/
        static/
        templates/
        __init__.py
        client_secrets.json
        database_setup.py
        lotsofitems.py
```
### Installing Software and Python Dependencies
The following programs are installed on the server.
* **git**: to clone the ```Book Catalog``` project from *GitHub*
* **pip**: to install some Python modules

The ```Book Catalog``` project was built using ```Flask```, which was installed via ```$ pip install Flask```.

The following Python modules and dependencies are installed on the server.
* **flask_sqlalchemy**: to work with *SQLAlchemy*
* **httplib2**: to provide web access via *HTTP*
* **psycopg2**: to connect and work with *PostgreSQL* server
* **oauth2client**: to work with *OAuth 2.0*
### Creating Configuration File
Virtual host configured in ```bookCatalog.conf``` file under ```/etc/apache2/sites-available/```.
```sh
<VirtualHost *:80>
                ServerName 54.191.192.22
                WSGIScriptAlias / /var/www/bookCatalog/bookcatalog.wsgi
                <Directory /var/www/bookCatalog/bookCatalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/bookCatalog/bookCatalog/static
                <Directory /var/www/bookCatalog/bookCatalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                DocumentRoot /var/www/html
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```
To enable the configured virtual host and disable the default Apache configuration, the following commands were used.
```sh
$ sudo a2ensite bookCatalog
$ sudo a2dissite 000-default.conf
```
### Creating WSGI File
The ```bookcatalog.wsgi``` file under ```/var/www/bookCatalog/``` is below.
```sh
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/bookCatalog/")

from bookCatalog import app as application
application.secret_key = 'alexandrabaturina'
```
### Pupulating the Database
Under ```catalog``` user, the empty ```catalogitems``` database is created.
```sh
$ CREATE DATABASE catalogitems;
```
For demonstration purposes, the ```catalogitems``` database is populated from the ```lotsoitems.py``` file using the following commands.
```sh
$ python3 database_setup.py
$ python3 lotsofitems.py
```
## List of Resourses
* [Flask mod_wsgi official documentation](https://flask.palletsprojects.com/en/1.1.x/deploying/mod_wsgi/#configuring-apache)
* [Official PostgreSQL documentation](https://www.postgresql.org/docs/9.5/auth-pg-hba-conf.html)
* [Git documentation](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [How to Set or Change the Time Zone in Linux](https://linuxize.com/post/how-to-set-or-change-timezone-in-linux/#changing-the-time-zone-in-linux)
* [How To Set Up a Firewall with UFW on Ubuntu 18.04](https://linuxize.com/post/how-to-setup-a-firewall-with-ufw-on-ubuntu-18-04/)
* [How to Deploy a Flask App to a Linux Server](https://www.youtube.com/watch?v=YFBRVJPhDGY&list=LLrB69AKIO0ESnb0rzqTZZ4g)
## Authors
Alexandra Baturina
