# Owncloud-Guide
A quick start guide to install configure and use owncloud


## Introduction
OwnCloud is an opensource filesharing application which provides secure file storage and synchronisation across devices. Files stored in ownCloud can be accessed from desktop or mobile clients using popular browser’s like Internet Explorer or Firefox.
This guide provides information about 
1.	ownCloud installation, 
2.	Addition of a User, 
3.	Steps to be followed for changing the port number and 
4.	Accessing ownCloud server from a desktop client.

The guide also includes sections for tips, which details out the possible hurdles an administrator may face in the installation and the minimal configuration of ownCloud described in this manual.
A test installation of ownCloud Server on Ubuntu 16.4, installed in a VirtualBox running on a Windows 8.1 desktop, was used in the preparation of this manual. Installation was done on Apache 2.4 web server using PHP 7.2 with access to MySQL Database.

## Installation of ownCloud

  ### Installation Prerequisites
  OwnCloud should be installed on a Unix based server but can be accessed from any Operating system. 
  More details about the system requirement can be found at https://doc.owncloud.org/server/latest/admin_manual/installation/system_requirements.html
  
  ### Installation Steps
  The steps involved in the Installation of ownCloud are 
1.	Enablement of the Environment
2.	Installation of Dependencies
3.	Creation of Database and Database user
4.	Installation of ownCloud
5.	Configuration of Apache Web server
6.	Completion of the installation from a Web Browser
  #### 1. Enablement of the Environment
  Owncloud requires the correct version of PHP, Database and Web server for it to work properly. LAMP Server which includes  Apache web server, PHP and MySQL Database can be chosen at the time of Ubuntu installation or can be installed before the setup using the command
  ```
  sudo apt-get install lamp server
  ```
  Note down the password created for MySQL.
  #### 2. Installation of Dependencies
  1. Login as root in Ubuntu
  ```
  sudo -i
  ```
  2. Install all needed PHP modules
  ```
  apt-get install -y apache2 mariadb-server libapache2-mod-php7.2 \ openssl php-imagick php7.2-common php7.2-curl php7.2-gd \
php7.2-imap php7.2-intl php7.2-json php7.2-ldap php7.2-mbstring \ php7.2-mysql php7.2-pgsql php-smbclient php-ssh2 \ 
php7.2-sqlite3 php7.2-xml php7.2-zip

  ```
  ```
  add-apt-repository ppa:ondrej/php
  ```
  ```
  apt-get update
  ```
  3.	Verify whether all the required PHP modules as listed in https://doc.owncloud.com/server/10.0/admin_manual/installation/source_installation.html are installed 
  ```
  php -m | grep -i <module_name> 
  ```
  4.	Edit php configuration file to increase the memory limit
  ```
  nano /etc/php/7.2/apache2/php.in
  ```
  Increase the memory to 256 MB by editing the line memory-limit =265 M

## Tips
*1.	The correct version of PHP should also be enabled and configured for ownCloud to load properly.
To check whether the correct version of PHP is installed, first check whether the php.ini file loaded is the correct one using the command* 
```
php -I | grep php.ini
```
*For example if 7.2 is the correct version to be used,To install the correct version of PHP use*
```
apt-get install php7.2
```
*To enable the correct version of PHP use the command*
```
a2enmod php7.2
```
*In order to avoid any conflict other versions of PHP should be disabled.
To disable any other version use the command*
```
a2dismod php 7.0
```
*2.	If all the modules are not loaded properly an error such as “PHP module gd not installed” may be generated. Any missing module should be installed before proceeding further. Since it is the gd module that is missing use*
```
apt-get install php7.2-gd
phpenmod gd 
```
#### 3.	Creation of Database and Database user 
1.	Login to the installed MySql server using the password created at the time of installation.
```
mysql -u root -p
```
2.	Create a database for ownCloud.
```
CREATE DATABASE owncloud;
```
3.	Create a system user for ownCloud in Mysql and Create a password for system user. 
```
CREATE USER ‘owncloud’@’localhost’ IDENTIFIED by ‘Create a password for system user’;
```
4.	Grant permission to system user.
```
GRANT ALL ON owncloud.*TO ‘owncloud’@’localhost’ WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```
#### 4.	Installation of ownCloud
1.	Get the registry key from ownCloud website
```
wget -nv  https://download.owncloud.org/download/repositories/production/Ubuntu_16.04/Release.key -O Release.key
apt-key add - < Release.key
```
2.	Add the registry key to the repository.
```
echo 'deb http://download.owncloud.org/download/repositories/production/Ubuntu_16.04/ /' > /etc/apt/sources.list.d/owncloud.list
```
3.	Install ownCloud.
```
apt-get install owncloud-files
apt-get update
```
#### 5.	Configuration of Apache Web server
1.  Modify Apache2 configuration file to reflect the location of the ownCloud files.
```
Nano/etc/apache2/sites.available/000-default.conf
```
![](https://github.com/Alex1-linux/Owncloud-Guide/blob/gh-pages/images/Apache1.JPG)

2.  Change the Document Root to point to the ownCloud files as shown by pointer 1 in the above figure.
3.	Restart apache2 server to reflect the changes.
```
Service apache2 restart
```
4.	Create an ownCloud configuration file in the sites.available directory in apache 2 files.
```
touch /etc/apache2/sites.available/owncloud.conf
```
5.	Add the following paths in it.
```
Alias /owncloud "/var/www/owncloud/"
<Directory /var/www/owncloud/>
  Options +FollowSymlinks
  AllowOverride All
 <IfModule mod_dav.c>
  Dav off
 </IfModule>
 SetEnv HOME /var/www/owncloud
 SetEnv HTTP_HOME /var/www/owncloud
</Directory>
```
6.	Create Symbolic links from the above configuration file to mods-enabled and sites-enabled directories in apache2 directory.
```
ln -s /etc/apache2/sites-available/owncloud.conf /etc/apache2/mods-enabled/owncloud.conf
ln -s /etc/apache2/sites-available/owncloud.conf /etc/apache2/sites-enabled/owncloud.conf
```
7.	Restart apache2 server to reflect the changes.
```
Service apache2 restart
```
8.	Enable the site and the apache modules with the following commands
```
a2ensite owncloud.conf
a2ensite rewrite
a2ensite headers
a2ensite env
a2ensite dir
a2ensite mime
```
9.	Restart apache2 server to reflect the changes.
```
Service apache2 restart
```
#### 6.	Completion of the installation from a Web Browser
To complete the installation, a connection from a browser to ownCloud server need to be established.
1.	Find out the ipaddress of the ubuntu server.
Ifconfig
2.	Open a browser and type in
http://ipaddress/owncloud
3.	In the page that opens up 

![](https://github.com/Alex1-linux/Owncloud-Guide/blob/gh-pages/images/welcome1.JPG)

    
   a.	Create an admin login. Enter a user name and password in the box provided. See pointer 1 in the above figure.
   
   b.	Click on “Storage & Database”. See pointer 2 in the above figure.
        Enter the name of the database and the system user name.
        
   c. Click the button “Finish setup” to complete the installation. See pointer 3 in the above figure.
   
 ## Tips 
*If an error “Cannot write into "config" directory!” is seen in the ownCloud webpage at the time of the completion of the installation, it means there is a permission issue in accessing the ownCloud configuration directory. This can be solved by giving permission to the HTTP system user.*

```
chown -R www-data:www-data /var/www/owncloud/
```




  
  
  
  
  
  
  
  
  
  
