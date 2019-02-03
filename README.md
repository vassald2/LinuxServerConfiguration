# LinuxServerConfiguration
The purpose of this project is to take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

# Public IP Address
http://100.25.61.195.xip.io/

# SSH IP and Port
 ssh -i *grader ssh key* grader@100.25.61.195 -p 2200
 
# Steps taken to complete project
 
## Step 1: Create Lightsail Instance
1. Navigate to Amazon Lightsail 
2. Create Instance/ OS Only/ Ubuntu distribution
3. Login to instance through SSH
4. "sudo apt-get update" to update packages

## Step 2: Change port and configure firewall
1. "sudo vi /etc/ssh/sshd_config"
2. Locate the line that says "Port 22" and change to "Port 2200"
3. "sudo service sshd restart" 
4. In Lightsail, go to the Networking tab for your instance and add a custom rule to access port 2200 over TCP and 123 over UDP
#### For Updating Firewall
5. "sudo ufw allow ssh"
6. "sudo ufw allow 2200"
7. "sudo ufw allow http"
8. "sudo apt-get install ntp"
9. "sudo ufw allow 123/udp"
10. "sudo ufw enable"

## Step 3: Give Grader Access
1. "sudo adduser grader"
2. "sudo visudo"
3. Add "grader ALL=(ALL:ALL) ALL" under the line that says "root ALL=(ALL:ALL) ALL"
4. In your server create a new directory and file to store your newly created public key e.g. mkdir .ssh sudo touch .ssh/authorized_keys
5. On your local machine use ssh-keygen to create a key pair
6. Paste  your newly created public key to the .ssh/authorized_keys file we created
7. Make sure you're logged in as grader and run "sudo chmod 700 .ssh" "sudo chmod 644 .ssh/authorized_keys" "sudo service ssh restart"
8. Login as grader using your private key to ensure it worked

## Step 4: Change Time Zone
1. sudo dpkg-reconfigure tzdata
2. "None of the above"
3. "UTC"

## Step 5: Prepare python project 
1. Using digital oceans tutorials really helped with this part.
2. Install Python and neccesary libraries: "sudo apt-get install libapache2-mod-wsgi python-dev" "sudo apt-get install apache2"
"sudo apt-get install python-setuptools libapache2-mod-wsgi" "sudo apt-get install git" "sudo apt-get install python"
3. Enable mod-wsgi: "sudo a2enmod wsgi"
4. Begin creating file tree for project: "cd /var/www"
5. "sudo mkdir Catalog" "cd Catalog" 
6. Clone my project in: "git clone *my git repo* Catalog"
7. Create file to serve app: "sudo vi catalog.wsgi" paste this: 
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/Catalog/")
from Catalog import app as application
application.secret_key = 'secret key'

8. Rename application.py to __init__.py: "mv application.py __init__.py"
### Install virtual environment 
9. "cd Catalog"
10. "sudo pip install virtualenv" "sudo virtualenv venv" "source venv/bin/activate"
11. Install all required packages for running the project e.g. sqlalchemy
12. "deactivate" to exit venv

File tree now looks like this:

    |----Catalog
    |---------catalog.wsgi
    |---------Catalog
    |--------------static
    |--------------templates
    |--------------__init__.py
    |--------------database_setup.py
    |--------------populate_database.py
    |--------------venv

## Step 6: Create database and modify code
1. Install packages: "sudo apt-get install libq-dev python-dev" "sudo apt-get install postgresql postgresql-contrib"
2. Open postgres console "sudo su - postgres psql"
3. "CREATE USER catalog WITH PASSWORD 'password';"
4. "CREATE DATABASE catalog WITH OWNER catalog;"
5. In the code any instance where a database engine is created replace it with 'postgresql://catalog:password@localhost/catalog'
6. Any instance in the code where "client_secret.json" is referenced replace with '/var/www/Catalog/Catalog/client_secret.json'

## Step 7: Create VirtualHost in Apache2
1. Create configuration for your app "sudo vi /etc/apache2/sites-available/Catalog.conf"
2. Copy and paste this code into file:

        <VirtualHost *:80>
                ServerName 100.25.61.195.xip.io
                ServerAdmin ubuntu@100.25.61.195
                ServerAlias ec2-100-25-61-195.compute-1.amazonaws.com
                WSGIScriptAlias / /var/www/Catalog/catalog.wsgi
                <Directory /var/www/Catalog/Catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/Catalog/Catalog/static
                <Directory /var/www/Catalog/Catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>  

3. Enable the project: "sudo a2ensite Catalog.conf"



## Step 8: Modify Google Oauth
1. Add 'xip.io' to you Authorized Domains
2. In Authorized Javascript add 'http://100.25.61.195.xip.io/'
3. In Authorized Redirect Urls add:

         http://100.25.61.195.xip.io/gconnect	
         http://100.25.61.195.xip.io/oauth2callback	
         http://100.25.61.195.xip.io/login
4. Download JSON and replace client_secret.json with newly constructed JSON

## Step 9: Restart and Test
1. Restart Apache: "sudo service apache2 restart"
2. Navigate to http://100.25.61.195.xip.io/ and begin testing

External Resources:
1. https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
2. https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04
3. Udacity FSND Discussion boards and Knowledge Hub





