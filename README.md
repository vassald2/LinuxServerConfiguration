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
8. 










