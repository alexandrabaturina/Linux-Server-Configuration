# Linux Server Configuration
**Linux Server Configuration** project of consists of the following steps.
- Initial configuring of **Ubuntu** Linux server instance on **Amazon Lightsail**
- Configuring Apache web server
- Configuring PostgreSQL database server
- Deploying [**Book Catalog**]() application

Verify the deployment by visiting http://54.191.192.22/.

## Securing Server
### Configuring Lightsail Firewall
### Configuring Uncomplicated Firewall
## Preparing for Deploying
## Configuring Server
### Configuring Timezone
The local timezone for **grader** is configured to UTC with the following command.
```sh
$ sudo timedatectl set-timezone UTC
```
### Installing and Configuring Apache
**Apache** web server is installed using the following command.
```sh
$ sudo apt-get install apache2
```
The **Book Catalog** project is built with Python 3, so the Python 3 mod_wsgi package is installed on the server to host **Book Catalog** as a mod_wsgi application.
```sh
$ sudo apt-get install libapache2-mod-wsgi-py3
```
### Installing and Configuring PostgreSQL
**PostgreSQL** database server is installed using the following command.
```sh
$ sudo apt-get install postgresql
```
To disable remote connections to a **PostgreSQL** database, in **PostgreSQL** client authentication configuration file  ```/etc/postgresql/9.5/main/pg_hba.conf``` listen addresses are set to ```127.0.0.1```.
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
#### Creating *catalog* user
Database user **catalog** with limited permissions to **Book Catalog** database is created via interactive terminal for working with Postgres using the following commands.
```sh
postgres=# create user catalog with login password 'catalog'; 
CREATE ROLE 
postgres=# alter user catalog with createdb; 
ALTER ROLE 
```
## Deploying Project
## List of Resourses
- [How to Set or Change the Time Zone in Linux](https://linuxize.com/post/how-to-set-or-change-timezone-in-linux/#changing-the-time-zone-in-linux)
- [How to Deploy a Flask App to a Linux Server](https://www.youtube.com/watch?v=YFBRVJPhDGY&list=LLrB69AKIO0ESnb0rzqTZZ4g)
- [Flask mod_wsgi official documentation](https://flask.palletsprojects.com/en/1.1.x/deploying/mod_wsgi/#configuring-apache)
## Authors
Alexandra Baturina
