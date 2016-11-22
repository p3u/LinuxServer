# Linux server configuration project for Udacity

## What I had to do

Take a baseline installation of a Linux distribution on a virtual machine and prepare it to host my Catalog web applications using Apache2.

## Challenges

The video classes covered the concepts, but were far away from a walkthrough. This was great because just like in real work, in order to finish the project, I had to do a lot of Googling and experimenting to learn.

## Tasks done in the project
Launch a Virtual Machine on AWS  
SSH into it  
Create a new user named grader  
Give the grader the permission to sudo  
Update all currently installed packages  
Change the SSH port from 22 to 2200  
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)  
Configure the local timezone to UTC  
Install and configure Apache to serve a Python mod_wsgi application  
Install and configure PostgreSQL, unallowing remote connections  
Create a new user named catalog with limited permissions to your catalog application database  
Install git, clone and setup your Catalog App project  

## Useful info to connect
IP address: 35.164.12.102  
Accessible SSH port: 2200  
You can test the WebApp (while it's still on AWS) in the URL: http://ec2-35-164-12-102.us-west-2.compute.amazonaws.com/  
The only way to access the server is using a private key provided in the repo (udacity_key.rsa)  
The password for the grader user is: Grader!951  

## A long yet incomplete list of things that I had to do
1. Logged in as root user through ssh
2. Created a user called grader using `$ sudo adduser grader`.
3. Gave user grader sudo powers by adding a new file under the suoders directory: `$ sudo nano /etc/sudoers.d/grader` with the text: "grader ALL=(ALL:ALL) ALL"
4. Configured the local timezone to UTC: `$ sudo dpkg-reconfigure tzdata`.
5. Logged out from the server and generated an encryption key using git bash with: `$ ssh-keygen -f ~/.ssh/udacity_key.rsa`.
6. Logged back into the server created the folder (.ssh) and the file (authorized_keys): `$ touch /home/grader/.ssh/authorized_keys`.
7. Added the key info to the created file then change some permissions:
8. `$ sudo chmod 700 /home/grader/.ssh`.
9. `$ sudo chmod 644 /home/grader/.ssh/authorized_keys`.
10. Changed the owner of the folder to *grader*: `$ sudo chown -R grader:grader /home/grader/.ssh`.
11. Logged out from the server and logged back in with *grader* via ssh with private key: `$ ssh -i ~/.ssh/udacity_key.rsa grader@35.164.12.102`.
12. Enforced key based authentication by editing sshd_config file: `$ sudo nano /etc/ssh/sshd_config`.
		*PasswordAuthentication* set to *no*.
13. Also changed ssh port to 2200 on the same file
14. Also disabled ssh login for *root*  on the same file. (*PermitRootLogin* set to *no*.)
15. `$ sudo service ssh restart`.
16. Using *ufw* I disabled every connection except ssh (2200/tcp) http (80/tcp) and udp(123/udp)
17. Installed Apache and mod_wsgi (an Apache HTTP server mod to serve Python Flask applications)
18. Enabled *mod_wsgi*: `$ sudo a2enmod wsgi`.
19. Started Apacje`$ sudo service apache2 start`.
20. Installed git, configured user name and cloned my Catalog application from github
21. Changed the owner of the application folder to www-data (so Apache could read write and execute) `sudo chown -R www-data:www-data /var/www`
22. Added user grader to www-data group so it could also have access `sudo adduser grader www-data`
23. Made a *catalog.wsgi* file to serve the application over the *mod_wsgi*.
24. Rewrote the application since some changes were necessary. You can see the changes in the new (Deployment Branch)[https://github.com/p3u/Catalog/tree/Deployment/]
		Some of the changes were: Rewriting the code to use PostgreSQL instead of SQLlite.
		Changing paths for img upload
		Changing paths for google json secret (oAuth)
25. Installed *pip*, so I could download a bunch of dependencies: `$ sudo apt-get install python-pip`.
26. Installed a virtualenv: `$ sudo pip install virtualenv`.
27. Create a virtual environment in the catalog app folder: `$ sudo virtualenv venv`.
28. Activated the virtual environment: `$ source venv/bin/activate`.
29. Changed permissions to the virtual environment folder: `$ sudo chmod -R 777 venv`.
30. Install Flask: `$ pip install Flask`.
31. Install all the other project's dependencies: `$ pip install bleach httplib2 request oauth2client sqlalchemy psycopg2`.
32. Configured the virtual host file: `$ sudo nano /etc/apache2/sites-available/catalog.conf`.
		Figured out I had to set an alias and permisions to my img/uploads folder. This took a while :).
33. Enable virtual host: `$ sudo a2ensite catalog`.
34. Installed dependencies for working with PostgreSQL: `$ sudo apt-get install libpq-dev python-dev`.
35. Installed PostgreSQL itself: `$ sudo apt-get install postgresql postgresql-contrib`.
36. Changed user to postgres `$ sudo su - postgres`, and entered PostgreSQL Env `$ psql`.
37. Created user *catalog* with the password pass: `# CREATE USER catalog WITH PASSWORD 'pass';`.
38. Gave *catalog* CREATEDB capability: `# ALTER USER catalog CREATEDB;`.
39. Created the 'catalog' database: `# CREATE DATABASE catalog WITH OWNER catalog;`.
40. Connected to the database: `# \c catalog`.
41. Revoke rights from public: `# REVOKE ALL ON SCHEMA public FROM public;`.
42. Granted rights to *catalog* user: `# GRANT ALL ON SCHEMA public TO catalog;`.
43. Made sure no remote connections were allowed by checking `$ sudo nano /etc/postgresql/9.3/main/pg_hba.conf`
44. Restarted the Server! `$ sudo service apache2 restart`.

## Resoucers used to complete the project
Basically a lot of Google Search + (Configuring Linux WebServers)[https://br.udacity.com/course/configuring-linux-web-servers--ud299/]
Websited I visited the most according to my history:

*Stackoverflow*:  
http://stackoverflow.com/questions/22062266/how-to-give-apache-permission-to-write-to-home-directory
http://stackoverflow.com/questions/1682440/permission-denied-error-with-django-while-uploading-a-file
http://stackoverflow.com/questions/21797372/django-errno-13-permission-denied-var-www-media-animals-user-uploads
http://stackoverflow.com/questions/9943042/htaccess-order-deny-allow-deny
http://stackoverflow.com/questions/4313323/how-to-change-owner-of-postgresql-database

*DigitalOcean*:  
https://www.digitalocean.com/community/questions/proper-permissions-for-web-server-s-directory
https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-12-04
https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps
https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04
https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

*PostgreSQL Docs*:  
https://www.postgresql.org/docs/9.0/static/plpython.html
