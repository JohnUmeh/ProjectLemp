# PROJECT_LEMP
## ** /2. IMPLIMENTING LEMP/**

**LEMP:** is an open-source web application stack used to develop web applications. The term LEMP is an acronym that represents L for the Linux Operating system, Nginx (pronounced as engine-x, hence the E in the acronym) web server, M for MySQL database, and P for PHP scripting language.

**Linux:**
Linux is an open-source operating system that you can install and configure to meet different application requirements. Linux sits at the first level of the LEMP stack and supports other components on the upper layers. It is used in deploying other components.

**Nginx:** It is a web server that follows an event-driven approach and handles multiple requests within one thread. It is the second layer of the LEMP stack and supports all Unix-like OS and windows partially. Web browser requests are handled by Nginx aiding communication with server and database.

**MySQL:**
A relational database management system and is the third layer of the LEMP stack. The LEMP model uses MySQL for storing, managing, and querying information in relational databases.Query refers to special instructions for manipulating data in a relational database with the SQL language.

**PHP:**
It is a scripting language that allows websites to run the elements of LEMP stack dynamically.


# **CREATION OF EC2 INSTANCE**

First step: Log on to the AWS Cloud Services and Create an EC2 Ubuntu Virtual Machine(VM) instance. When creating the instance, choose keypair aunthentication and download private key (*.pem) on your local computer.

![connectinstance](https://user-images.githubusercontent.com/77943759/217891326-a15d060f-7043-4979-8d0f-be7cb10935b0.png)


# **CONFIGURING SECURITY GROUP INBOUND RULES ON EC2 INSTANCE**
A security group is a group of rules that acts as a virtual firewall to the type of traffic that enters or leaves an instance-inbound and outbound traffics respectively.

We need to enable our inbound traffic from anywhere in order to ensure our webpage accessibility on the internet.

![inbound](https://user-images.githubusercontent.com/77943759/217893711-462f0a36-7e77-4f42-adde-f1380b90e65b.png)


We can check the web server accessibility on the internet with curl alongside the IP address or DNS name of our local host.

`curl http://localhost:80 or curl http://127.0.0.1:80`

# ** INSTALL NGINX**
We install Nginx, our web server for displaying webpages through Ubuntu package manager `apt`
```
  
  sudo apt update
  
sudo apt install nginx

```
![nginxinstall](https://user-images.githubusercontent.com/77943759/217895346-d4bd013b-b408-43c9-9c8b-6ce4c86466e8.png)

To verify successful Nginx install

`sudo systemctl status nginx`

![checknginx](https://user-images.githubusercontent.com/77943759/217896349-8c74e4dc-bb3e-46de-be1d-a2974980ae43.png)

To test how our Nginx server can respond to requests from the Internet, open a web browser of your choice and try to access following url

`http://<Public-IP-Address>:80`

![nginxwelcome](https://user-images.githubusercontent.com/77943759/218227781-2fe74345-260b-40c1-a8d1-4289f18c7232.png)

The above page shows nginx is properly installed and accessible through firewall

To check the ip address without always opening the instance page, run the below code line

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

# **INSTALLING MySQL**
MySQL is a relational database which we use to store and manage data on the site

Use `sudo apt install mysql` command to install it and when prompted if you want to continue, type `Y` for yes

![mysqlinstall](https://user-images.githubusercontent.com/77943759/217896917-d52ed2f3-6b4e-48fa-9289-983ea5a9c67b.png)



Use `sudo mysql_secure_installation` command remove unwanted default permissions and enable database protection

![interactivescript](https://user-images.githubusercontent.com/77943759/217898246-3e17f16d-bfee-4a5c-a938-e1867f163d25.png)



To access the mysql Database, enter this on the terminal
`sudo mysql`  
The page below will appear showing that mysql is properly installed and connect from the administrative root

![mysqlopen](https://user-images.githubusercontent.com/77943759/218244719-d8a02b99-f1ee-4823-b8a8-09cf5477392b.png)


simply type `exit` to leave the MYSQL terminal


# **PHP AND ITS MODULES INSTALLATION**

PHP serves as the programming language responsible for dynamically displaying contents of the webpage to users who make request to the server

 Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. Also, we need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, we need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies. This code will download all of the two
 
 `sudo apt install php-fpm php-mysql`
 
 ![phpanddependencies](https://user-images.githubusercontent.com/77943759/218246125-2e91834d-c6b8-435d-bf54-2756692766b9.png)


# **CONFIGURING NGINX TO USE PHP PROCESSOR**

we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use projectLEMP as an example domain name.
# **Creating web Domain**

First, we create the directory for projectlamp using the mkdir command
`sudo mkdir /var/www/projectlampstack`

Next, we assign ownership of the directory with the current system user:

 `sudo chown -R $USER:$USER /var/www/projectlamp`
 
 ![Screenshot from 2023-02-05 23-38-17](https://user-images.githubusercontent.com/77943759/216852500-c1319e10-d4f1-481c-922d-fc76c3902e6e.png)

 
 Then, create and open a new configuration file in Apache’s sites-available directory

`sudo vi /etc/apache2/sites-available/projectlamp.conf`

copy and paste the following in he directory, it is the setting required to spin the server block
```
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
![Screenshot from 2023-02-05 23-40-54](https://user-images.githubusercontent.com/77943759/216852786-5790e68c-ac94-4edc-8cb8-2b70e3d80331.png)

Ater this, hit `esc` then type :wq to save and exit the vi editor

Next, run `sudo a2ensite projectlampstack` to activate the server block

You need to deactivate the default server block that is defaultly on apache:

`sudo a2dissite 000-default`

Now reload the apache2 server:

`sudo systemctl reload apache2`

To test that the server is working as expected, we create an index.html file in /var/www/projectlampstack

![Screenshot from 2023-02-05 23-58-12](https://user-images.githubusercontent.com/77943759/216853477-93a49201-e706-45b8-a71f-d52ad285893e.png)

Now, open any browser of your choice and type in 

`http://<public_ip_address>:80`

![Screenshot from 2023-02-06 00-05-00](https://user-images.githubusercontent.com/77943759/216854171-f57a2d5a-5581-42a7-af51-04e95a170ebd.png)

# **ENABLE PHP ON WEBSITE**

By default, index.html will take preceedence over index.php, to change this behaviour, open the editor

`sudo vim /etc/apache2/mods-enabled/dir.conf`

Then, paste this command

```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

![Screenshot from 2023-02-06 01-02-45](https://user-images.githubusercontent.com/77943759/216858925-029ae13c-0ad2-4d74-9b13-c4aa97e41b9c.png)


Reload apache for changes to take effect

`sudo systemctl reload apache2`

Create an index.php file

`vim /var/www/projectlamp/index.php`

Input this:
```
<?php
phpinfo();
```

![Screenshot from 2023-02-06 01-09-33](https://user-images.githubusercontent.com/77943759/216859517-1d2afed3-66d8-4560-a253-29b073847303.png)

Refresh your opened page or repeat 

`http://<public_ip_address>:80`


![Screenshot from 2023-02-06 01-11-29](https://user-images.githubusercontent.com/77943759/216859664-d47bcbab-979a-4bc9-837e-3e45e4e2d33a.png)

End
