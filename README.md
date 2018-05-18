## Linux Server configuration


### Project Overview
	A baseline installation of a Linux server and prepare it to 
	host web applications, secure server from a number of 
	attack vectors, install and configure a database server.

### Why this project?
	A deep understanding of exactly what a web application 
	do, how they are hosted, and the interactions between 
	multiple systems. This project helps to turn a brand-new,
	bare bones, Linux server into the secure and efficient web 
	application host the applications needs.

### Link to [Item Catalog](https://github.com/RahulKathuria/Item-Catalog)

* Ip Address: 35.154.240.35
* SSH Port : 2200
## Steps to configure the server 
1. Create an account on Amazon Web Services.
2.  When you have been successfully logged in, launch the virtual machine and SSH into the instance.
3. Download the private key and move it to the .ssh folder.
4. To make the public key usable and secure, input 
	```  $ chmod 600 ~/.ssh/AWSKey.pem```
5. Now login into the server using ``` $ ssh -i ~/.ssh/AWSKey.pem ubuntu@35.154.240.35```
6. Now we have to add a new user grader after becoming the root user. ``` $ sudo adduser grader```  will add a new user named grader.
7. To give sudo access to the grader, make a new file under etc directory using this ``` 
$ sudo nano /etc/sudoers.d/grader``` and add this text to that file : ```grader ALL=(ALL:ALL) ALL```.

8. Run these commands to update all packages and install finger.
	*   `$ sudo apt-get update`
	*	 `$ sudo apt-get upgrade`
	*    `$ sudo apt-get install finger` 
9.  Open a new terminal and write this command in that terminal :  ```$ ssh-keygen -f ~/.ssh/udacity_key.rsa ```
10. To read that public key, we have to use the cat command : ```$ cat ~/.ssh/udacity_key.rsa.pub```
11.  Copy this key, and now in the first terminal, we have to go to the grader's folder located under the home directory by ```  $ cd /home/grader```.
12. Now we have to create a ssh directory there by using ``` $ mkdir .ssh``` and creating a file to store that public key : ``` $ touch .ssh/authorized_keys```
13. Paste that key that you copied from the second terminal into this file by editing it using ``` $ nano .ssh/authorized_keys``` 
14. Now start the service again using ```$ sudo service ssh restart```
15.  Now we can login into the remote VM through ssh using this command : ```ssh -i udacity_key.rsa grader@35.154.240.35```
16. Change the permissions using `$ sudo chmod 700 /home/grader/.ssh` and `$ sudo chmod 644 /home/grader/.ssh/authorized_keys`.
17.  To enforce the password based authentication, change the text after PasswordAuthentication to no, Port line to 2200 and PermitRootLogin to no after running the command ```$ sudo nano /etc/ssh/sshd_config``` and restart the service again using the command written in point 14.
18. Now to configure the uncomplicated firewall, run these commands:
	  *  `$ sudo ufw allow 2200/tcp`
	  *  `$ sudo ufw allow 80/tcp`
   	  *  `$ sudo ufw allow 123/udp`
  	  *  `$ sudo ufw enable`
 
19. Now to deploy the application, run the following commands to install the necessary packages:
	* ``` $ sudo apt-get install apache2 libapache2-mod-wsgi git```
	* ``` $ sudo a2enmod wsgi```
	* ``` $ sudo apt-get install libpq-dev python-dev```
	* ``` $ sudo apt-get install git```
	* ``` $ sudo apt-get install postgresql postgresql-contrib```
	* ``` $ sudo apt-get install python-pip```
	* ``` $ sudo pip install Flask```
	* ``` $ sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils```
20. Now restart the service again,  ``` sudo service apache2 restart```
21. Now to deploy the application, we have to clone it in a separate directory.
22. Write down the following commands in the terminal : 
		* ``` $ sudo mkdir /var/www/catalog```
		* ``` $ sudo chown -R grader:grader catalog```
		* ``` $ cd catalog```
		* ``` $ git clone https://github.com/RahulKathuria/Item-Catalog.git```
23. Now we have to create a catalog.wsgi file: 
	*  ```$ touch catalog.wsgi```
	* ``` $ nano catalog.wsgi```
24. Now write this code in the file: 
 import sys 
 import logging
 logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")
from catalog import app as application
application.secret_key = 'supersecretkey'

 25. Rename the application from project.py to __init__.py
 26. Now to install the virtual machine, run the following commands:-   
*  ```$ sudo pip install virtualenv```
*  ```$ sudo virtualenv venv```
*  ```$ source venv/bin/activate```
*   ```$ sudo chmod -R 777 venv```

27. Now change the client_secrets.json line to /var/www/catalog/client_secrets.json
28. To enable the virtual host, use this command : 
		* -   `$ sudo nano /etc/apache2/sites-available/catalog.conf`
		* Paste the following code into that file : 
```<VirtualHost *:80>
    ServerAdmin admin@35.154.240.35
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
29. Now we have to create a user to create and set the database,
30. -   Create a new User named  catalog :  `# CREATE USER catalog WITH PASSWORD 'rahulkathuria';`
    
-   Create a new DB named  _catalog_:  `# CREATE DATABASE catalog WITH OWNER catalog;`
    
-   Connect to the database  _catalog_  :  `# \c catalog`
    
-    `# REVOKE ALL ON SCHEMA public FROM public;`
    
-    `# GRANT ALL ON SCHEMA public TO catalog;`
    
-   Log out from PostgreSQL:  `# \q`. Then return to the  grader  user:  `$ exit`
31. Use ``` sudo nano``` to edit files on the server by chaging every occurrence of ```engine = create_engine('sqlite:///restaurantmenu.db') ``` to ```engine = create_engine('postgresql://catalog:rahulkathuria@localhost/catalog)```
32. Now run ```$ lotsofmenu.py``` to populate the database with the list items.
33. Now, at the end we have to restart the apache server by ``` sudo service apache2 restart```
34. Now open the browser and type 35.154.240.35 and you are ready to go.


## Sources used :
 1. [Flask Documentation](http://flask.pocoo.org/docs/0.12/installation/)
 2. [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-14-04-lts)
 3. Stack Overflow