Project 5 - Linux Server Configuration version 1.0 10/06/2016

Description

In this project we deployed the web application "items catalog" from Project 3. 
Udacity and Amazon have provided a virtual server in Amazon’s Elastic Compute Cloud (EC2) for use for this project. 
Now the application is  accessible to the public!

Server Details

Server IP Address: 52.36.190.19
Server SSH Port: 2200
Application URL: http://ec2-52-36-190-19.us-west-2.compute.amazonaws.com/

Software Installed
1) Apache2
2) PostgreSQL
3) finger
4) Python-Pip
5) libapache2-mod-wsgi
6) postgresql-contrib
7) git
8) psycopg2
9) Flask-SQLAlchemy
10) Flask
11) google-api-python-client
12) virtualenv

Configuration Changes
1) Update all currently installed packages
	sudo apt-get update
	sudo apt-get upgrade

2) Create a new user named grader
	adduser grader

3) Give the user grader sudo access
	Create the file: grader under /etc/sudoers.d/ through "vi /etc/sudoers.d/grader", that contains the following content 

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	grader ALL=(ALL) NOPASSWD:ALL

4) Configure the local timezone to UTC.
	sudo dpkg-reconfigure tzdata

5) Change the SSH port from 22 to 2200
	sudo vi /etc/ssh/sshd_config
	
	Thwn change the Port from 22 to 2200

6) Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
	sudo ufw allow 2200/tcp
	sudo ufw allow http
	sudo ufw allow ntp
	sudo ufw enable

7) Install and configure Apache to serve a Python mod_wsgi application
	sudo apt-get install python-pip apache2 libapache2-mod-wsgi

8) Install and configure PostgreSQL
	sudo apt-get install postgresql

9) Create a new user named catalog that has limited permissions to your catalog application database
	sudo su - postgres
	psql
	createuser
	Enter name of role to add: catalog
	Enter password for new role:
	Enter it again:
	Shall the new role be a superuser? (y/n) n
	Shall the new role be allowed to create databases? (y/n) n
	Shall the new role be allowed to create more new roles? (y/n) n

10) Create the catalog database
	psql
	CREATE DATABASE catalog;
	\q

11) Protect .git directory
	sudo chmod 700 /var/www/FlaskApp/FlaskApp/.git

12) Create a wsgi file entry point to work with mod_wsgi
	sudo vi /etc/apache2/sites-available/FlaskApp.conf

	<VirtualHost *:80>
                ServerName http://ec2-52-36-190-19.us-west-2.compute.amazonaws.com
                ServerAdmin admin@mywebsite.com
                WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
                <Directory /var/www/FlaskApp/FlaskApp/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/FlaskApp/FlaskApp/static
                <Directory /var/www/FlaskApp/FlaskApp/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog /var/www/FlaskApp/error.log
                LogLevel warn
                CustomLog /var/www/FlaskApp/access.log combined
	</VirtualHost>

	
13) Update Database connection string in database_setup.py to the use PostgreSQL rather than SQLite3
	sudo vi /var/www/FlaskApp/FlaskApp/database_setup.py

	then update the database connection string to: postgresql://catalog:catalog@localhost/catalog

14) Restart Apache2:
	sudo service apache2 restart
	
15) Grader user password is:
	ud@city2012!

Creator:

Elie Shalhoub
