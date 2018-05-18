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
9.  Open a new terminal and write this command in that terminal : $ ```ssh-keygen -f ~/.ssh/udacity_key.rsa ```
10. To read that public key, we have to use the cat command : ```$ cat ~/.ssh/udacity_key.rsa.pub```
11.  


