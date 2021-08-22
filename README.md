<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary><h2 style="display: inline-block">Table of Contents</h2></summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
    </li>
    <li>
      <a href="#ubuntu-virtual-machine">Ubuntu Virtual Machine</a>
    </li>
    <li><a href="#installing-lamp-stack-on-ubuntu">Installing LAMP stack on Ubuntu</a></li>
    <li><a href="#installing-suitecrm-on-ubuntu">Installing SuiteCRM on Ubuntu</a></li>
    <li><a href="#edit-hosts-file-on-ubuntu">Edit Hosts File on Ubuntu</a></li>
    <li><a href="#renaming-top-bar-menu-and-modules-suitecrm">Renaming Top Bar Menu and Modules SuiteCRM</a></li>
    <li><a href="#install-dolibarr-on-ubuntu">Install Dolibarr on Ubuntu</a></li>
    <li><a href="#create-suitecrm-docker-container">Edit Hosts File on Ubuntu</a></li>
    <li><a href="#create-dolibarr-docker-container">Edit Hosts File on Ubuntu</a></li>
    <li><a href="#create-suitecrm-docker-container">Edit Hosts File on Ubuntu</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project




<!-- GETTING STARTED -->
## Ubuntu Virtual Machine







<!-- INSTALLING LAMP STACK -->
## Installing LAMP stack on Ubuntu

> LAMP stands for Linux, Apache, MySQL, and PHP. Together, they provide a proven set of software for delivering high-performance web applications. Each component contributes essential capabilities to the stack.

[Learn more about LAMP](https://www.ibm.com/cloud/learn/lamp-stack-explained)

### Step 1: Update Packages

Before anything else, it's a good practice to always update repository and software packages. Run the following commands on your Ubuntu 20.04 OS.


```console
sudo apt update && sudo apt upgrade
```

### Step 2: Install Apache Web Server

Enter the following command to install Apache Web server. 

```console
sudo apt install -y apache2 apache2-utils
```
In most cases, Apache should automatically start after installation. You can check its status using the following command.

```console
systemctl status apache2
```
The output should look like this:
```console
apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2021-08-11 11:31:31 CST; 2s ago
       Docs: https://httpd.apache.org/docs/2.4/
    Process: 53003 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
   Main PID: 53011 (apache2)
      Tasks: 55 (limit: 19072)
     Memory: 6.4M
     CGroup: /system.slice/apache2.service
             ├─53011 /usr/sbin/apache2 -k start
             ├─53012 /usr/sbin/apache2 -k start
             └─53013 /usr/sbin/apache2 -k start
```
You can exit this output text by pressing Q.

If Apache isn't running after installation, the following command can be used.

```console
systemctl start apache2
```

You can now try if Apache Web Server is running properly by typing "localhost" or "127.0.0.1" on your browser address bar.
A webpage  should be displayed saying "It works".


Now we need to set www-data (Apache user) as the owner of document root (otherwise known as web root). By default it’s owned by the root user.
Use the following command.

```console
sudo chown www-data:www-data /var/www/html/ -R
```

We need to check if the global ServerName is already configure using the following command.

```console
sudo apache2ctl -t
```
If the output looked like the following text, it means that the global ServerName should be configured first.

```console
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1. Set the 'ServerName' directive globally to suppress this message
```
Enter the following command to open a command-line text editor and edit the content of the configuration.

```console
sudo nano /etc/apache2/conf-available/servername.conf
```
Add the following line in this file.
```console
ServerName localhost
```
To save a file in Nano text editor, press Ctrl+O, then press Enter to confirm. To exit, press Ctrl+X. Then enable this config file.
```console
sudo a2enconf servername.conf
```
Now reload Apache.
```console
sudo systemctl reload apache2
```
Enter agin the command sudo apache2ctl -t to check if the previous error is already solved.
```console
sudo apache2ctl -t
```
### Step 3: Install MariaDB
Enter the following commands.
```console
sudo apt install mariadb-server mariadb-client
```
After installation, check if mariadb is already running.
```console
systemctl status mariadb
```
The output should look like this:
```console
 mariadb.service - MariaDB 10.3.22 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2021-08-11 14:19:16 UTC; 18s ago
       Docs: man:mysqld(8)
             https://mariadb.com/kb/en/library/systemd/
   Main PID: 9161 (mysqld)
     Status: "Taking your SQL requests now..."
      Tasks: 31 (limit: 9451)
     Memory: 64.7M
     CGroup: /system.slice/mariadb.service
             └─9161 /usr/sbin/mysqld
```
If mariadb is not running, enter this command:
```console
sudo systemctl start mariadb
```
Now run the post-installation security script.
```console
sudo mysql_secure_installation
```
Then it will ask to enter MariaDB root password, press Enter key as the root password isn’t set yet. Then enter y to set the root password for MariaDB server.
The following questions can be set to default by pressing enter until the setup is done.

You may want to try entering the mariadb console by typing the following command.
```console
sudo mariadb -u root -p
```
To exit:
```console
exit;
```
### Step 4: Install PHP7.4
Install PHP7.4 and other modules by entering this command.
```console
sudo apt install php7.4 libapache2-mod-php7.4 php7.4-mysql php-common php7.4-cli php7.4-common php7.4-json php7.4-opcache php7.4-readline
```
Enable the Apache php7.4 module then restart Apache Web server.
```console
sudo a2enmod php7.4
```

```console
sudo systemctl restart apache2
```
Now we need to create a info.php file in the document root directory.
```console
sudo nano /var/www/html/info.php
```
Add this to the file:
```console
<?php phpinfo(); ?>
```
Press Ctrl+O to save, then press Enter to confirm. To exit, press Ctrl+X
On you browser address bar, type localhost/info.php or 127.0.0.1/info.php to check the your server's PHP information.






<!-- INSTALLING SUITECRM -->
## Installing SuiteCRM on Ubuntu

### Step 1: Download SuiteCRM onto Your Ubuntu 20.04 Server
```console
wget https://suitecrm.com/files/162/SuiteCRM-7.11/525/SuiteCRM-7.11.18.zip
```
Install the unzip utility and unzip it to /var/www/ directory.
```console
sudo apt install unzip
```
```console
sudo mkdir -p /var/www/
```
```console
sudo unzip SuiteCRM-7.11.18.zip -d /var/www/
```
Make the name of the directory simplier.
```console
sudo mv /var/www/SuiteCRM-7.11.18/ /var/www/suitecrm
```
Change directory by typing: 
```console
cd /var/www/suitecrm
```
Then run the following commands to set the correct permissions.
```console
sudo chown -R www-data:www-data /var/www/suitecrm/
```
```console
sudo chmod -R 755 .
```
```console
sudo chmod -R 775 cache custom modules themes data upload
```
```console
sudo chmod 775 config_override.php 2>/dev/null
```
### Step 2: Create a MariaDB Database and User for SuiteCRM
Log in to MariaDB console.
```console
sudo mysql -u root
```
Create a new database for SuiteCRM. You can replace "suitecrm" according to your own preference.
```console
CREATE DATABASE suitecrm;
```
Grant all permissions  of the the database to the user by entering the following command. Replace the "suitecrm", "user" and "password" with the name of you database that you created in the previous step, and username and password according to your preference.

```console
GRANT ALL ON suitecrm.* TO 'user'@'localhost' IDENTIFIED BY 'password';
```
Flush privileges table and exit MariaDB console.
```console
FLUSH PRIVILEGES;
```
```console
EXIT;
```
### Step 3: Install Required and Recommended PHP Modules.
```console
sudo apt install php-imagick php7.4-fpm php7.4-mysql php7.4-common php7.4-gd php7.4-imap php7.4-json php7.4-curl php7.4-zip php7.4-xml php7.4-mbstring php7.4-bz2 php7.4-intl php7.4-gmp
```
Disable the PHP module and prefork MPM module in Apache.
```console
sudo a2dismod php7.4
```
```console
sudo a2dismod mpm_prefork
```
Run the following command to enable three modules in order to use PHP-FPM in Apache
```console
sudo a2enmod mpm_event proxy_fcgi setenvif
```
Restart Apache
```console
sudo systemctl restart apache2
```
### Step 4: Create Apache Virtual Host for SuiteCRM
```console
sudo nano /etc/apache2/sites-available/suitecrm.conf
```
Put the following text into the file. Replace suitecrm.example.com with your real domain name if you have one.
```console
<VirtualHost *:80>
  ServerName suitecrm.example.com
  DocumentRoot /var/www/suitecrm/

  ErrorLog ${APACHE_LOG_DIR}/suitecrm_error.log
  CustomLog ${APACHE_LOG_DIR}/suitecrm_access.log combined

  <Directory />
    Options FollowSymLinks
    AllowOverride All
  </Directory>

  <Directory /var/www/suitecrm/>
    Options FollowSymLinks MultiViews
    AllowOverride All
    Order allow,deny
    allow from all
  </Directory>

Include /etc/apache2/conf-available/php7.4-fpm.conf

</VirtualHost>
```
Save and close the file. Then enable this virtual host.
```console
sudo a2ensite suitecrm.conf
```
Then reload Apache.
```console
sudo systemctl reload apache2
```
Run the following command to change the minimum and amximum upload size.
```console
sudo sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 20M/g' /etc/php/7.4/fpm/php.ini
```
Then restart PHP-FPM and Apache.
```console
sudo systemctl restart php7.4-fpm
```
```console
sudo systemctl restart apache2
```
### Step 5: Finish Installation using the Web browser
Type in the following on your web browser address bar to see the web-based intall wizard. Provide information to the required fields to complete the setup.
```console
http://localhost/install.php
```
or
```console
http://127.0.0.1/install.php
```
<!-- EDIT HOSTS -->
## Edit Hosts File on Ubuntu

### Step 1: Open the hosts file with your text editor
You can use any other text editor that you have like Nano or Vim. In this case, I am using Gedit.
```console
sudo -i gedit /etc/hosts
```
The file should look like this:
```console
127.0.0.1       localhost
127.0.1.1       marl-VirtualBox

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
### Step 2: Add new entry below localhost following the same format.
You can change the suitecrm.example.com according to your preference.
```console
127.0.0.1       suitecrm.example.com
```
Save and close the file.



## Renaming Top Bar Menu and Modules SuiteCRM

### Step 1: Go to Admin Section
This can be found by clicking the person icon on the top right corner then select admin from the dropdown menu.


![admin](https://user-images.githubusercontent.com/88755620/129151370-daffe077-5efa-482b-b61a-f94df24223f8.png)


### Step 2: Edit the Top Menu Bar
Under the developer tools, select Configure Module Menu Filters.


![admin2](https://user-images.githubusercontent.com/88755620/129152361-3a565440-86e8-4bc1-926b-21726de29063.png)

Then start modifying the labels. You can also delete or add to the top bar menu. Click Save and Deploy to save the changes.

![admin3](https://user-images.githubusercontent.com/88755620/129153075-45829928-2f66-4a12-b48f-d79d38ae0180.png)

### Step 3: Edit Labels of Modules
Go back to developer tools and select Rename Modules.


![admin5](https://user-images.githubusercontent.com/88755620/129154423-7867d6df-5f8d-4c95-9c98-e49e1289e501.png)


Start renaming the modules according to your preference.


![admin4](https://user-images.githubusercontent.com/88755620/129154038-e4da6873-5127-4b8e-b38c-4400c010db4f.png)



## Install Dolibarr on Ubuntu

### Step 1: Create a database for Dolibarr
Enter this command to access the mariadb console then enter the password.
```console
sudo mysql -u root -p
```
Create a database. You can change the "dolibarr" with anything you want.
```console
CREATE DATABASE dolibarr;
```
Create a database user called dolibarruser with new password. You can also change this according to your preference.
```console
CREATE USER 'dolibarruser'@'localhost' IDENTIFIED BY 'password';
```
Grant the user full access to the database.
```console
GRANT ALL ON dolibarr.* TO 'dolibarruser'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
```
### Step 2: Download Dolibarr
```console
cd /tmp && wget https://sourceforge.net/projects/dolibarr/files/Dolibarr%20ERP-CRM/7.0.2/dolibarr-7.0.2.zip
```
Unzip and move Dolibarr to the appropriate directory by entering these commands:
```console
unzip dolibarr-7.0.2.zip
```
```console
sudo mv dolibarr-7.0.2 /var/www/html/dolibarr
```
Provide permissions for Dolibarr to function.
```console
sudo chown -R www-data:www-data /var/www/html/dolibarr/
```
```console
sudo chmod -R 755 /var/www/html/dolibarr/
```
Create Apache configuration file for dolibarr.
```console
sudo nano /etc/apache2/sites-available/dolibarr.conf
```
Add the follwing to the file. Change the values of ServerAdmin ,ServerName and DocumentRoot. Save and exit.
```console
<VirtualHost *:80>
     ServerAdmin admin@example.com
     DocumentRoot /var/www/html/dolibarr/htdocs
     ServerName example.com
     
     <Directory /var/www/html/dolibarr/htdocs/>
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
     </Directory>

     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```
Enable the configuration for dolibarr.
```console
sudo a2ensite dolibarr.conf
```
```console
sudo a2enmod rewrite
```
Then restart Apache2
```console
sudo systemctl restart apache2.service
```
### Step 3: Finish installation with Web-based install wizard.
Type in example.com, localhost/ or 127.0.0.1 on your browser address bar.


![doli1](https://user-images.githubusercontent.com/88755620/129172219-511405d2-9fa4-425f-8ebe-7ebc499682a4.PNG)


![doli2](https://user-images.githubusercontent.com/88755620/129172255-26e76905-99be-43c9-87d8-507b206db4e4.PNG)


![doli3](https://user-images.githubusercontent.com/88755620/129172272-c1084286-8a84-4993-8c30-d11bbdd429b2.PNG)
