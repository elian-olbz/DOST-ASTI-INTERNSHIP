<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary><h2 style="display: inline-block">Table of Contents</h2></summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
      </ul>
    </li>
    <li><a href="#installing-lamp-stack-on-ubuntu">Installing LAMP stack on Ubuntu</a></li>
    <li><a href="#installing-suitecrm-on-ubuntu">Installing SuiteCRM on Ubuntu</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project




<!-- GETTING STARTED -->
## Getting Started

### Prerequisites




<!-- INSTALLING LAMP STACK -->
## Installing LAMP stack on Ubuntu

> LAMP stands for Linux, Apache, MySQL, and PHP. Together, they provide a proven set of software for delivering high-performance web applications. Each component contributes essential capabilities to the stack.

[Learn more about LAMP](https://www.ibm.com/cloud/learn/lamp-stack-explained)

## Step 1: Update Packages

Before anything else, it's a good practice to always update repository and software packages. Run the following commands on your Ubuntu 20.04 OS.


```console
sudo apt update && sudo apt upgrade
```

## Step 2: Install Apache Web Server

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
## Step 3: Install MariaDB
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
## Step 4: Install PHP7.4
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


## Installing SuiteCRM on Ubuntu
