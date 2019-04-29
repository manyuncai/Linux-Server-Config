###Project 6: Linux Server Configuration ![CI status](https://img.shields.io/badge/build-passing-brightgreen.svg)

<b>Linux Server Configuration </b> This project is set up and secure a Linux server to host one of the Fullstack web application  <b>Item Catalog App</b> we built in lesson 3 The backend: Databases & Application. A new Ubuntu Linux server instance on Amazon LightSail is configured to host the Flask application with  Apache, python and PostgreSQL server.  


### Repository location
Linux Server Configuration
* public IP: 18.224.62.191
* port: 2200
* Catalog App: https://github.com/manyuncai/catalog
* Linux Readme: https://github.com/manyuncai/Linux-Server-Config/blob/master/readme.md


### Step 1
* Start a new Ubuntu Linux server instance on <a href = "https://lightsail.aws.amazon.com/ls/webapp/home/instances">Amazon Lightsail </a>. I create account and pick the lowest price plan, create an instance my-project-6 as OS only with Ubuntu.

### Step 2
* Follow the instructions provided to SSH into your server.

### Step 3
* Update all currently installed packages: at ubuntu@ip-private-IP:$ run

  `$ sudo apt-get update`
  `$ sudo apt-get upgrade`

### Step 4  
* Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it; in lightsail instance, under Networking, change firewall  
http to 80, add custom tcp port 123 and 2200, romove port 22

### Step 5
* Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123). At ubuntu@ip-private-IP:$ run

 `$ sudo ufw default deny incoming`
 `$ sudo ufw default allow outgoing`
 `$ sudo ufw allow 2200`
 `$ sudo ufw allow 80`

 `$ sudo ufw allow 123`
* modify /etc/ssh/sshd_config file, change listen port from 22 to 2200 and restart sshd_config

 `$ sudo nano /etc/ssh/sshd_config`
 `$ sudo systemctl restart ssh`

* enable ufw and check status ensure only port 2200, 80 and 123 are allowed

 `$ sudo ufw enable`
 `$ sudo ufw status`

* ssh lightsail session is frozen at this time, follow  <a href= "https://lightsail.aws.amazon.com/ls/docs/en/articles/lightsail-how-to-set-up-putty-to-connect-using-ssh"> lightsail document </a> to download and setup PuTTy to connect using SSH in Amazon LightSail. Note at Auth window, check Bypass authentication entirely (ssh-2 only), uncheck Display pre-authentication banner (ssh-2 only), login as ubuntu, and continue to follow steps

### Step 6
* Give grader access.
* In order for your project to be reviewed, the grader needs to be able to log in to your server.
* Create a new user account named grader.

  `$ sudo adduser grader`
  Password: grader
  Full Name: Udacity Grader

### Step 7
* Give grader the permission to sudo.

 `$ sudo adduser grader`
Add line :
grader ALL=(ALL) NOPASSWD:ALL
Save file (ctrl o ) and exit (ctrl x)

### Step 8
*  Create an SSH key pair for grader using the ssh-keygen tool:
  I am in my local machine, git bash to c:/user/manyu/.ssh/, run command

  `$ sudo adduser grader`

  Enter file in which to save the key (/c/Users/manyu/.ssh/id_rsa): /c/users/manyu/.ssh/key

  Enter passphrase (empty for no passphrase):

  Enter same passphrase again:

  Your identification has been saved in /c/users/manyu/.ssh/key.

  Your public key has been saved in /c/users/manyu/.ssh/key.pub.
* in local terminal, I change the private key to key.pem with
`$ mv key key.pem `
* In local terminal use Cat to copy content of the key.pub by `$ cat key.pub `

* Back to lightsail ubuntu@ip-private-ip:~$ terminal and switch to grader terminal:  `$sudo su - grader`

* now in grader@ip-private-ip:~$ run:

 `$ mkdir .ssh`

 `$ touch .ssh/authorized keys`

 `$ nano .ssh/authorized keys` paste local host key.pub content to this file, save & exit

 `$ chmod 700 .ssh `  Use the chmod command to change the .ssh directory's permissions to 700

 `$ chmod 644 .ssh/authorized keys ` Use the chmod command to change the .ssh/authorized_keys file permissions to 644

 `$ chmod 600 .ssh/authorized keys ` Use the chmod command to change the .ssh/authorized_keys file permissions to 600, with instruction from <a href="https://aws.amazon.com/premiumsupport/knowledge-center/new-user-accounts-linux-instance/"> Amazon Doc </a>

* in grader@ip-private-ip:~$ Forcing the key base authentication by changing the passwordAuthentication to no, and restart the ssh

 `$ sudo nano /etc/ssh/sshd_config `

 `$ sudo service ssh restart `

* In my local machine, verify I can login as grader with the public ip address from lightsail

 `$ ssh —i /c/users/manyu/.ssh/key.pem gradera18.224.62.191 —p 2200 `

### Step 9
* Prepare to deploy your project.
* Configure the local timezone to UTC.
* Switch back to ubuntu@ip-private:~$ terminal with `$ Sudo su ubuntu` from grader terminal
`sudo dpkg-reconfigure tzdata` pick UTC from the list

### step 10
* Install and configure Apache to serve a Python mod_wsgi application. In ubuntu@ip-private:~$ terminal run:

 ` $ sudo apt-get install apache2`

 ` $ sudo apt-get install libapache2-mod-wsgi`

 ` $ sudo apt-get install python-setuptools`

 ` $ sudo service apache2 restart`

### Step 11
* Install and configure PostgreSQL. In ubuntu@ip-private:~$ terminal run:

 ` $ sudo apt-get install postgresql`

* Do not allow remote connections  - verify the file is for local connection only

 ` $ sudo nano /etc/postgresql/9.5/main/pg_hba.conf`

* Create a new database user named catalog that has limited permissions to your catalog application database. Switch to postgres mode to create a new database(catalog), user(catalog) and set password(catalog).

 ` $ sudo su - postgres`

 ` postgres $ psql`

 ` postgres=# CREATE DATABASE catalog;`

 ` postgres=# CREATE USER catalog;`

 ` postgres=# ALTER ROLE catalog WITH PASSWORD 'catalog'`

 ` postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog`

 ` postgres=# ALTER USER catalog PASSWORD 'catalog'`

* Exit postgres by \q

### Step 12
*  Install git, to clone my remote repository, In ubuntu@ip-private:~$ terminal run:

 ` $ sudo apt-get install git`

### Step 13
* Deploy the Item Catalog project. Follow <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps"> Digital Ocean Doc </a> instruction in this step. In ubuntu@ip-private:~$ terminal run this commands

* Enable the wsgi ` $ sudo a2enmod wsgi`
* move to dir `cd /var/www `
* Create the application directory structure using `$ sudo mkdir catalog`
* change to dir `cd/catalog`
* Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program. After clone project, my dir structure look as Digital ocean doc suggested.

 `$ git clone https://github.com/manyuncai/catalog `

 * change to dir after clone project `cd catalog `
 * In this directory: change project.py to __init__.py with `sudo mv project.py  __init__.py`
 * with `sudo nano __init__.py` and `sudo nano database_set.py`, change engine to
  engine = create_engine ('postgresql://catalog:catalog@localhost/catalog')
 * install  and upgrade flask_sqlalchemy ` $ sudo pip install flask_sqlalchemy` & `$ sudo pip install --upgrade pip`
 * install oauth2clint ` sudo pip install --upgrade google-api-python-client oauth2clint`
 * install requests ` sudo pip install requests`
 * install sqlalchemy-utils `sudo pip install sqlalchemy-utils`
 * Run python __init__.py and database_setup.py, verify no more errors: Run python __init__.py and database_setup.py, verify no more errors: `sudo python database_setup.py` & `sudo python __init__.py`

### step 14
* Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser.
* In ubuntu@ip-private:~$ terminal run: `$ sudo nano /etc/apache2/sites-available/catalog.conf`
```
<VirtualHost *:80>
                ServerName 18.224.62.191
                ServerAdmin manyuncai@yahoo.com
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

* Enabled the virtual host with command: `$ sudo a2ensite catalog`
* Create the .wsgi file:
 `$ cd /var/www/catalog`
 `$ sudo nano catalog.wsgi`
```
 #!/usr/bin/python
 import sys
 import logging
 logging.basicConfig(stream=sys.stderr)
 sys.path.insert(0,"/var/www/catalog/")

 from catalog import app as application
 application.secret_key = 'super_secret_key'
```
* Restart Apache: `$ sudo service apache2 restart`

### Contributing
* <a href="https://aws.amazon.com/premiumsupport/knowledge-center/new-user-accounts-linux-instance/"> Amazon Doc </a>
* <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps"> Digital Ocean Doc </a>

### Known Bugs
completed all 14 steps to configure server and catalog app, no error in log file. However I am not seeing the app running in the public ip address. Today is the due date for the project, I am submitting for review and suggestion and I can correct the issue.
