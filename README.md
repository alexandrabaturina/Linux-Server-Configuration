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
### Configuring Apache
**Apache** web server is installed with the following command.
```sh
$ sudo apt-get install apache2
```
The **Book Catalog** project is built with Python 3, so the Python 3 mod_wsgi package is installed on the server to host **Book Catalog** as a mod_wsgi application.
```sh
$ sudo apt-get install libapache2-mod-wsgi-py3
```
### Configuring PostgreSQL
## Deploying Project
## List of Resourses
- [How to Set or Change the Time Zone in Linux](https://linuxize.com/post/how-to-set-or-change-timezone-in-linux/#changing-the-time-zone-in-linux)
- [How to Deploy a Flask App to a Linux Server](https://www.youtube.com/watch?v=YFBRVJPhDGY&list=LLrB69AKIO0ESnb0rzqTZZ4g)
- [Flask mod_wsgi official documentation](https://flask.palletsprojects.com/en/1.1.x/deploying/mod_wsgi/#configuring-apache)
## Authors
Alexandra Baturina
