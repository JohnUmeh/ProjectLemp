# 02.PROJECT_LEMP
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

First, we create the directory for projectLEMP using the mkdir command

`sudo mkdir /var/www/projectLEMP`

Next, we assign ownership of the directory with the current system user:

 `sudo chown -R $USER:$USER /var/www/projectLEMP`
 
 ![mkdir](https://user-images.githubusercontent.com/77943759/218274992-2412815b-fcbf-493d-950d-7235fddaafd2.png)

 
 Then, create and open a new configuration file in Nginx sites-available directory using nano command line editor

`sudo nano /etc/nginx/sites-available/projectLEMP`

copy and paste the following bare-bones configuration in the directory;

```
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

```
![editvar](https://user-images.githubusercontent.com/77943759/218275944-4047ded6-f17c-4a92-a49e-59b39657b044.png)


Ater this, type ctrl O to save, enter and ctrl X to exit

Activate the configuration by linking to the config file from Nginx’s sites-enabled directory:

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

This will tell Nginx to use the configuration next time it is reloaded. Type `sudo nginx -t` to check for syntax error. The page below confirms there is no syntax error

![nginxtest](https://user-images.githubusercontent.com/77943759/218276359-20787878-7cb4-4661-a73a-75fae9671a96.png)

We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

Next, reload nginx with `sudo systemctl reload nginx`


To test that the server is working as expected, we create an index.html file in  location /var/www/projectLEMP;

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

![echonginx](https://user-images.githubusercontent.com/77943759/218276795-e95c7b12-ae15-4779-aa54-09ffc176f304.png)


Now, open any browser of your choice and type in 

`http://<public_ip_address>:80`

![lempecho](https://user-images.githubusercontent.com/77943759/218276966-9fd2d5c3-305e-4faf-a559-a775f1554c65.png)

The above page shows the server working properly.


# **TESTING PHP WITH NGINX**

Now that LEMP stack is fully installed, test to see if it can handle .php files properly

To do this, we use the nano command editor to create a test php file in the document root

Create the file `sudo nano /var/www/projectLEMP/info.php`

Paste this in the open file

```
<?php
phpinfo();
```
Now enter http://`server_domain_or_IP`/info.php

![phpinfo](https://user-images.githubusercontent.com/77943759/218278407-ee553885-de97-4c42-8c70-032406b3cc86.png)

This page shows the info of the php page

# **RETRIEVING DATA FROM MySQL DATABASE WITH PHP**

We create a database next so that the Nginx website will be able to query data from the database and display

We will create a database named example_database and a user named example_user

Connect mysql

`sudo mysql`

Create a new database by entering this command into the mysql console

`CREATE DATABASE `example_database`;`

![dbcreate](https://user-images.githubusercontent.com/77943759/218281506-02f78549-9bd9-4a9e-9777-d0756ccca6af.png)

Next, create a new user and grant him full privileges on the database you have just created. Enter this into the mysql console

`CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password12';`

Enter the following into mysql consoleto grant the created user full priviledge

`GRANT ALL ON example_database.* TO 'example_user'@'%';`

Type `exit` to exit the mysql console

Next You can test if the new user has the proper permissions by logging in to the MySQL console again by using custom user credentials:

`mysql -u example_user -p`. Enter the required password 

Now acess your database with `SHOW DATABASES;`

The page below displays the database

![showdatabases](https://user-images.githubusercontent.com/77943759/218281813-e9f6b073-ad07-4527-a093-259c5f4c55ac.png)

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:
```
CREATE TABLE example_database.todo_list ( 
item_id INT AUTO_INCREMENT,
content VARCHAR(255),
PRIMARY KEY(item_id)
);
```
![createtable](https://user-images.githubusercontent.com/77943759/218282033-e776b25b-10ab-440b-9795-bff9068ee103.png)

We add a few lines to the table by typing into the mysql console:

```
INSERT INTO example_database.todo_list (content) VALUES ("lemp1code");
INSERT INTO example_database.todo_list (content) VALUES ("lemp2code");
INSERT INTO example_database.todo_list (content) VALUES ("last lemp message");
```
Then `run SELECT * FROM example_database.todo_list;` to view the db which will bring the page below

![databasemessage](https://user-images.githubusercontent.com/77943759/218282200-7bee223b-da02-4b3e-89ec-a5cae6b7c86a.png)

exit mysql by typing in `exit` in the console

Now we can create a PHP script that will connect to MySQL and query for our content. Create a new PHP file in the custom web root directory using nano editor:

`nano /var/www/projectLEMP/todo_list.php`

Next, copy and paste in the file:

```
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
Save and close then visit

`http://<Public_domain_or_IP>/todo_list.php`

The following page display shows the contents of the file created

![fullpage](https://user-images.githubusercontent.com/77943759/218282478-e6f9474c-d80f-4c9f-a2ee-5fdf7a3b069a.png)

with this, we have successfully used Nginx as web server and Mysql as database to provide PHP websites and visitors' applications.

END
