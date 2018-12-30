# Linux Server Configuration Project

This is the last project for us as a students of Udacity Full Stack Web Developer Nanodegree program.
 
## Introduction

This project is a good tutorial on how to build a secure server, install and configure a database server, and deploy a web application into it.

## Project Overview

A baseline installation of a Linux server [Amazon Lightsail](https://signin.aws.amazon.com) and prepare it to host [Item Catalog](https://github.com/kalthommusa/Item_Catalog_Project2) web applications.

## IP Address : 
   35.180.75.141

## The complete URL to the hosted web application: 
   http://35.180.75.141.xip.io

## Make an Amazon Lightsail Instance

  1. First, log in to [Amazon Lightsail](https://signin.aws.amazon.com). If you don't already have an Amazon Web Services account, you'll be prompted to create one.

  2. Create an instance. Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.

  3. Choose an instance image: Ubuntu Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating system and optionally built-in applications. There are two settings to make here. First, choose "OS Only" (rather than "Apps + OS"). Second, choose Ubuntu as the operating system.

  4. Choose your instance plan. The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, the lowest tier of instance is just fine

  5. Give your instance a hostname. Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. 

  6. It's running; let's use it! Once your instance has started up, you can log into it with SSH from your browser. 

The public IP address of the instance is displayed along with its name.

My public IP address of the instance is : 35.180.75.141

## Instance Configuration and Connection 

  1. Download your instance Private Key from your profile, which starts with "LightsailDefaultKey" and with ".pem" extention.

  2. In your local machine, rename the file to "Lightsail_Key.rsa" and save it in `~/.ssh/`

  3. Change the permission: `chmod 600 ~/.ssh/Lightsail_key.rsa`

  4. Connect to our Amazon Lightsail instance by using Ubuntu User `ssh -i ~/.ssh/Lightsail_Key.rsa -p 22 ubuntu@[PUT YOUR PUBLIC IP ADDRESS, IN MY CASE IS 35.180.75.141]`

  **Congrats!** , you officially now have access to the server.

## Secure the Server

  1. Update all currently installed packages, run the following commands :
     * `sudo apt-get update`
     * `sudo apt-get upgrade`

  2. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
     * Open SSH config , run the following command :
     `sudo nano /etc/ssh/sshd_config`

     * Change the "Port 22" in the file to "Port 2200"

     * Restart SSH service, run the following command :
     `sudo service ssh restart`


  3. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123), run the following commands :
     * `sudo ufw allow 2200`
     * `sudo ufw allow 80`
     * `sudo ufw allow 123`

     Then enable ufw, run the following command :
     * `sudo ufw enable`

  4. In Amazon Lightsail, go to "Networking" section then "Firewall" :  
     * Disable SSH - TCP - port range 20
     * Add Custom - TCP - port range 2200
     * Add Cusotm - UDP - port range 123
     * LEAVE HTTP - TCP - port range 80 AS IT IS 

**Warning**: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.


Now that SSH port has been changed to 2200, Try exiting the SSH connection and re-connecting to your server, run the following command :

    ssh -i ~/.ssh/Lightsail_Key.rsa -p 2200 ubuntu@35.180.75.141

    Congrats!


## Create a New User "grader"

   1. To create a user called grader, run the following command :

      * `sudo adduser grader`
      set a password , then you can fill the rest of the informations or just leave it empty by press entr it is optional.

   To check if the new user has been created successfully or not, run the following command :

      * `ls /home/grader` 
      If the command exits without any problems, then that means the path is valid.

   2. To give grader sudo permission, run the following command :

      * `sudo nano /etc/sudoers.d/grader`
      and then add the following line :
      * `grader ALL=(ALL:ALL) ALL`
      

## Generate the SSH key-pairs for grader     

   1. To create the public and private keys for grader, open another terminal in your local machine out of the server 'or disconnent with your server', then run the following command :
      * `ssh-keygen` 
        when you asked to name the file that saves the keys, leave the path as it is but you can change the last directory's name, choose "grader_key" or whatever you want , in my case it is : /c/Users/Toshiba/.ssh/grader_key

      * `cd to ~/.ssh`

      * Change the permission, run the following commands :
        `chmod 644 ~/.ssh/grader_key`

      * Copy the public key from your local machine, in my case : 
        `cat /c/Users/Toshiba/.ssh/grader_key.pub`

      * Back to your server terminal 'or connenct to your server run: `ssh -i ~/.ssh/Lightsail_Key.rsa -p 2200 ubuntu@35.180.75.141` ', and run the following commands :

        `cd /home/grader`
      
      * Create .ssh folder for saving the public keys, run the following commands :

        `sudo mkdir .ssh`

      * `sudo touch .ssh/authorized_keys`

      * Paste the public key in your virtual machine, run the following command : 

        `sudo nano .ssh/authorized_keys`

      * Change the permission, run the following commands :

        `sudo chmod 700 /home/grader/.ssh`
        `sudo chmod 644 /home/grader/.ssh/authorized_keys`
      
      * Change file owner, run the following command :

        `sudo chown -R grader:grader /home/grader/.ssh`

      * To force all users to only login using a key pair, edit the configuration file for sshd, run the  following command :

        `sudo nano /etc/ssh/sshd_config`
        then change PasswordAuthentication to **no** 

      * Restart server configuration, run the following command :

        `sudo service ssh restart`

      * Disconnect and re-connecting to your server but this time login as a grader user with the generated key , run the following command :

        `ssh -i ~/.ssh/grader_key -p 2200 grader@35.180.75.141`
        
        **Congrats!**

## Prepare the Server to Deploy a Wed Application

   1. Configure the local timezone to UTC, run the following command :

      * `sudo dpkg-reconfigure tzdata`
      Select "None of the above" and then select UTC

   2. Install Apache, run the following command :

      * `sudo apt-get install apache2`

   3. TO configure Apache,install mod_wsgi, run the following command :

      * `sudo apt-get install python-setuptools libapache2-mod-wsgi`

   4. Restart Apache, run the following command :

      * `sudo service apache2 restart`

   5. Install python2, run the following command :

      * `sudo apt-get install python2.7`

   6. Install PostgreSQL, run the following command : 

      * `sudo apt-get install postgresql`

   7. Install psycopg2, run the following command :

      * `sudo apt-get -qqy install postgresql python-psycopg2`

   8. Setup the database, run the following commands : 
  
      * `sudo apt-get install libpq-dev python-dev`

      * `sudo apt-get install postgresql postgresql-contrib`

   9. Check that no remote connections are allowed (default), run the following command :  

      * `sudo nano /etc/postgresql/9.5/main/pg_hba.conf`

   10. Create a new database user named catalog, run the following commands :

      * `sudo su - postgres`

      * `psql`

      then inside psql shell, run the following commands :

      * `CREATE USER catalog WITH PASSWORD 'choose a password in my case it is catalog';`

      * `ALTER USER catalog CREATEDB;`

      * `CREATE DATABASE catalog WITH OWNER catalog;`

      then exit psql shell
      `\q`
      and then logout from postgres 

   11. Install git, run the following command :

       * `sudo apt-get install git`


## Deploy the Item Catalog project

  1. Clone the Item Catalog project from the Github repository, run the following commands : 
     * `cd /var/www`

     * `sudo mkdir catalog`

     * `sudo chown -R grader:grader catalog`

     * `cd catalog`

     * `git clone https://github.com/kalthommusa/Item_Catalog_Project2 catalog`

  2. Create .wsgi file in the same directory, run the following command :
     * `sudo nano catalog.wsgi` 

     then add the follwoing inside it: 
     
     `import sys
     import logging
     logging.basicConfig(stream=sys.stderr)
     sys.path.insert(0, "/var/www/catalog/") `
     `                                       `
     `from catalog import app as application
     application.secret_key = 'Put_Your_google_secret_key'`
  
  3. Reastart Apache, run the following command :
    * `sudo service apache2 restart`

  4. cd to `/var/www/catalog/catalog`

  5. Rename the application.py file, run the following command :
    `sudo mv project.py __init__.py`

  6. Edit __init__.py, run the following command :
    * `sudo nano __init__.py`
    then change `app.run(host='0.0.0.0', port=5000)` to `app.run()`

  7. Change the database in  __init__.py, database_setup.py and populate_database.py files :
    from `engine = create_engine('sqlite:///weddingvenuesappwithusers.db')` to
    `engine = create_engine('postgresql://catalog:catalog@localhost/catalog')` with username catalog and password catalog, run the following commands then edit each file's database :

    `sudo nano __init__.py`
    `sudo nano database_setup.py`
    `sudo nano populate_database.py`

  8. Edit __init__.py, run the following command :

      `sudo nano __init__.py`
    then change `client_secrets.json` path to `/var/www/catalog/catalog/client_secrets.json`
  
  9. cd to `~/` then installing the virtual machine, run the following commands :
    
     * `sudo apt-get install python-pip`
     * `sudo pip install virtualenv`
     * `sudo virtualenv venv`
     * `source venv/bin/activate`
     * `sudo chmod -R 777 venv`

  10. Install Flask and other packages, run the following commands :

     * `sudo pip install psycopg2-binary`
     * `sudo pip install psycopg2t`
     * `sudo pip install Flask-SQLAlchemy`
     * `sudo pip install requests`
     * `sudo pip install oauth2client`

  11. Setup server configuration, create catalog.conf "Flask App", run the following command :
    `sudo nano /etc/apache2/sites-available/catalog.conf`
    then write the following lines:

`<VirtualHost *:80>
    ServerName [YOUR PUBLIC IP ADDRESS IN MY CASE 35.180.75.141]
    ServerAlias [YOUR AMAZON LIGHTSAIL HOST NAME IN MY CASE Udacity_Linux_Server_Configuration]
    ServerAdmin admin@[YOUR PUBLIC IP ADDRESS IN MY CASE 35.180.75.141]
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
</VirtualHost>`

  12. Enable the virtual host, run the following command :

   * `sudo a2ensite catalog`
   
   
## Update Google Configuration

  1. Go to google console and add authorized domains xip.io

  2. Add authorized Javascript origins: http://35.180.75.141.xip.io

  3. Add authorised redirect URIs: http://35.180.75.141.xip.io/gconnect and http://35.180.75.141.xip.io/login

  4. Downlaod the updated JSON file, copy its content and paste it in your server, run the following command :
    * `sudo nano client_secrets.json`

  5. restart ssh server, run the following command :
    * `sudo service apache2 restart`

## Congrats! 
   
   Now you can access your web app open up http://35.180.75.141.xip.io  in the browser and use it