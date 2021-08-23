<details open="open">
  <summary><h2 style="display: inline-block">Table of Contents</h2></summary>
  <ol>
    <li><a href="#ubuntu-virtual-machine">Ubuntu Virtual Machine</a> </li>
    <li><a href="#installing-lamp-stack-on-ubuntu">Installing LAMP stack on Ubuntu</a></li>
    <li><a href="#installing-suitecrm-on-ubuntu">Installing SuiteCRM on Ubuntu</a></li>
    <li><a href="#edit-hosts-file-on-ubuntu">Edit Hosts File on Ubuntu</a></li>
    <li><a href="#renaming-top-bar-menu-and-modules-suitecrm">Renaming Top Bar Menu and Modules SuiteCRM</a></li>
    <li><a href="#install-dolibarr-on-ubuntu">Install Dolibarr on Ubuntu</a></li>
    <li><a href="#create-suitecrm-docker-container">Create SuiteCRM Docker Container</a></li>
    <li><a href="#create-dolibarr-docker-container">Create Dolibarr Docker Container</a></li>
    <li><a href="#edit-dolibarr-for-vendor-management">Edit Dolibarr for Vendor Management</a>
    <ul>
        <li><a href="#filtering-data">Filtering Data</a></li>
        <li><a href="#mass-email">Mass Email</a></li>
        <li><a href="#purchase-order-format">Purchase Order Format</a></li>
        <li><a href="#labels">Labels</a></li>
        <li><a href="#customers-and-prospects">Customers and Prospects</a></li>
      </ul>
    </li>
  </ol>
</details>


<!-- Ubuntu VM -->
## Ubuntu Virtual Machine

### Step 1: Download Oracle VirtualBox
Download [VirtualBox](https://www.virtualbox.org/wiki/Downloads) from their official website.
Look for the platform package that is compatible for your operating system.

### Step 2: Install VirtualBox
Go to the location where you downloaded VirtualBox and doubble-clickit to run and start the installation.
The setup window will appear.Click the "next" button to proceed.
Choose the location where you want to install VirtualBox the click "next".
You can leave the next page as default and click next. Click "yes" to install.

### Step 3: Download Ubuntu
Download [Ubuntu](https://ubuntu.com/download/desktop) from their official website.
Look for the Ubuntu 20.04 LTS that is compatible for your operating system.


### Step 4: Create Virtual Machine 
Open VirtualBox and click on the "New" button.
Give name for your machine with its location. Select the type of your machine as Linux under the drop-down menu and the version as Ubuntu.

### Step 5: Allocate the Memory
You can set the RAM based on the recommended memory size. These can still be changed after you finished the setup process.
Click "next" to proceed.

Select "Create a virtual hard disk now" option and click on the "Create" button to proceed.
Choose the "VDI (VirtualBox Disk Image)" option and click "Next".
Click "next" again given that the selected option is "Dynamically allocated".
Select the amount of space for your virtual machine and click the "Create" button.

### Step 6: Attach the ISO
Select the Virtual Machine that you created and click "start" button with the green arrow icon on the top.
A "select start-up disk" screen will appear in which you will be able to mount the ISO file that you downloaded earlier.
Click the folder icon on the right to locate the ISO. Click "start" to begin the installation.

### Step 7: Finish installation with the install wizard
Select your language and click "Install Ubuntu"
Select your desired keyboard layout.
Use the default option as "Normal Installation" with the "Download updates while installing Ubuntu" and click on the "Continue" button.
Select the default option as the "Erase disk and Install Ubuntu" and click on the "Install Now" option to proceed.
A warning prompt will appear on the screen and click on the "Continue" button to ignore this warning.
Choose your time zone on the map and click Continue.
Set your user account here by filling the necessary details and click on the "Continue" button to proceed.
Now, the installation process will begin.
After the installation completed, click "restart now".

### Step 8: Change the Network Setting
Change the Network adapter so that your virtual machine can connect to the internet.
On the upper-left corner, go to Machine -> Settings -> Network.
Under the Adapter 1 there is a drop-down menu named "Attached to:". Select "Bridged Adapter" and select the name of your adapter.
Click "ok" to save.



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




## Create SuiteCRM Docker Container
 
### Step 1: Download SuiteCRM docker image
```console
$ docker pull bitnami/suitecrm:latest
```

### Step 2: Create a network

```console
$ docker network create suitecrm-network
```

### Step 3: Create a volume for MariaDB persistence and create a MariaDB container

```console
$ docker volume create --name mariadb_data
$ docker run -d --name mariadb \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_suitecrm \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_suitecrm \
  --network suitecrm-network \
  --volume mariadb_data:/bitnami/mariadb \
  bitnami/mariadb:latest
```

### Step 4: Create volumes for SuiteCRM persistence and launch the container

```console
$ docker volume create --name suitecrm_data
$ docker run -d --name suitecrm \
  -p 8080:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env SUITECRM_DATABASE_USER=bn_suitecrm \
  --env SUITECRM_DATABASE_PASSWORD=bitnami \
  --env SUITECRM_DATABASE_NAME=bitnami_suitecrm \
  --network suitecrm-network \
  --volume suitecrm_data:/bitnami/suitecrm \
  bitnami/suitecrm:latest
```

Access your application at *http://your-ip/*

### Persisting your application


### Step 1: Create a network (if it does not exist)

```console
$ docker network create suitecrm-network
```

### Step 2. Create a MariaDB container with host volume

```console
$ docker run -d --name mariadb \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_suitecrm \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_suitecrm \
  --network suitecrm-network \
  --volume /path/to/mariadb-persistence:/bitnami/mariadb \
  bitnami/mariadb:latest
```

### Step 3. Create the SuiteCRM container with host volumes

```console
$ docker run -d --name suitecrm \
  -p 8080:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env SUITECRM_DATABASE_USER=bn_suitecrm \
  --env SUITECRM_DATABASE_PASSWORD=bitnami \
  --env SUITECRM_DATABASE_NAME=bitnami_suitecrm \
  --network suitecrm-network \
  --volume /path/to/suitecrm-persistence:/bitnami/suitecrm \
  bitnami/suitecrm:latest
```



## Create Dolibarr Docker Container

The Dolibarr package that you downloaded includes a .yml file that you can use to deploy dolibarr on a docker container. This .yml file is a text file containing the set of instructions to create the docker image of dolibarr. If you don't have dolibarr, click [here](https://www.dolibarr.org/downloads.php) to download.

### Step 1: Change Directory
Change your working direcotry to where you have unziped dolibarr. The yml file is located inside /build/docker on the folder of dolibarr that you unziped. Enter the following command to change directory. In my case, the dolibarr folder can be found at:
```console
cd /var/www/html/dolibarr-13.0.4/build/docker
```
Now show the list of files inside the directory to make sure that the yml file is there by typing ls.
```console
ls
```
### Step 2: Use the yml to create the docker image
```console
sudo docker-compose up -d
```
This process will take a couple of minutes. Once the process is done, you can list the docker containers that was created and already running by entering this command:
```console
sudo docker ps
```
### Step 3: Finish the installation with the web-based install wizard
On your address bar, type:
```console
127.0.0.1
```
or
```console
localhost/
```
Provide the required information. Most of the fields are already filled automatically. Click next to proceed.

![doli1](https://user-images.githubusercontent.com/88755620/129210194-449fe53b-3a08-4b6c-8148-e2dffdf13ee4.PNG)

Use the name of your mariadb docker container that is running under the database server field. Click next.

![doli2](https://user-images.githubusercontent.com/88755620/129210319-6af8baeb-17bc-4987-a55d-49afc951547a.PNG)

![doli3](https://user-images.githubusercontent.com/88755620/129210329-f878dcb4-4263-45bc-b1c7-03c90748006e.PNG)

Setup the admin credentials that you will use to log in. Click next then log in to dolibarr.

![doli4](https://user-images.githubusercontent.com/88755620/129210340-e17563fd-8963-41fc-a91b-0ea220a4fec1.PNG)

Finish the installation by filling in the Company/Organization

![doli5](https://user-images.githubusercontent.com/88755620/129210493-155c0c95-db58-4391-9940-da83b33df705.PNG)




## Edit Dolibarr for Vendor Management

## Filtering Data
Third parties in Dolibarrr include Vendors, Customers and Prospects. Having the capability to filter these data can be very efficient and convenient for the person using Dolibarr.
There are 2 ways that we can use to filter the third party data in Dolibarr. The first one is through dictionary and the other one is using the tag module.
## Method 1: Adding new Third-paty type on Dictionaries Setup
### Step 1: Go to Third-party types
Go to Home -> Setup -> Dictionaries -> Third-party types

### Step 2: Add new Third-party type
Provide the empty fields such as Code, Labeland Country with anything you want. Click save to the new third-party.

![last1](https://user-images.githubusercontent.com/88755620/129547709-9f057781-5c7f-453a-9ba3-4b54b18d6f69.PNG)

### Step 3: Select this Third-party type
If you have an existing third-party you can attach this third-party type by modifying it. You can find this on the lower portion of the webpage. you can also label a new third that you are going to create with this feature. Click save for changes to be saved.

![Screenshot (11)](https://user-images.githubusercontent.com/88755620/129548986-08815109-dc06-4082-a543-4cf154c07a34.png)

### Step 4: Filter the third-parties
Select the third-party type that you cretaed and click on search icon on the upper-right corner.

![Screenshot (12)](https://user-images.githubusercontent.com/88755620/129549743-2b92a230-9090-414a-ba4a-7141c6dfd5be.png)

![last2](https://user-images.githubusercontent.com/88755620/129549960-3b6d4bcb-66f9-450c-8f8e-043327ef2aa4.PNG)
You can see that the number (5) on the list of third parties changed (1) indicating that only one third party is labeled with the third-party type that I created in this tutorial.
## Method 2: Using the Tag module to filter Third-parties
### Step 1: Enable the Module
Go to Home -> Setup -> Modules/Application -> Multi-modules tools. Enable the module.

### Step 2: Add/create new tag
You can find the interface of tag module under the Third-parties. After clicking third-parties on the top bar menu, look for it on the lower-left part of the webpage.

![last3](https://user-images.githubusercontent.com/88755620/129551734-4131d137-ca6f-4eee-ba66-d20ac8242f5f.PNG)

Click on New tag/category to create one.

Enter the Reference name that you want and you can leave other fields empty. Click on Create this tag/category to proceed.


![last4](https://user-images.githubusercontent.com/88755620/129552286-f6567b1f-f158-45a7-9054-a2a2415bcba4.PNG)

You can find a dropdown menu on the lower part. Select the third-party that you want to add this tag to. Click on Add to tag/category to save.

![Screenshot (13)](https://user-images.githubusercontent.com/88755620/129552680-8c4512a7-11d3-4c12-ad45-56097d8a2eb7.png)

### Step 3: Filter third parties
Go to list third-parties and on the upper menu, you can find the Vendors tag/category. Select the tag that you created.

![Screenshot (14)](https://user-images.githubusercontent.com/88755620/129553467-c6e41269-acbf-418c-b7f3-9e1a98bd22c2.png)

Click on the search button on the upper-right corner to show the list of third-parties with this tag/category.

![last5](https://user-images.githubusercontent.com/88755620/129553484-82f4a9d2-86f1-48cb-83c5-bba4ed625f74.PNG)

## Mass Email
### Step 1: Enable Mass Emailing Module

Go to Home -> Setup -> Modules/Application -> Interfaces with external systems
You can find the interface of this module under the Tools on the top bar menu. 

### Step 2: Create a new Emailing Card
Below the Emailing section, click on "new emailing" to create a new one. 
Provide the basic parts of the email like description, sender, etc. Click on the "recipients"tab after you finished the emailing card.

### Step 3: Add the recipients
There are 5 ways to add the reciepients of email in dolibarr. To add multiple recipients from third parties and contacts, you can use the tags drop-down menu which you created from the previous tutorial.
While the others are from dolibarr user database, email from file and emails input by user.

## Purchase Order Format

## Lables

## Customers and Prospects



