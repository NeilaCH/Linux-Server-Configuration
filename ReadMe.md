# Linux Server Configuration

This project is for the Full Stuck Udacity [Nanodegree program](https://www.udacity.com/nanodegree).
The project consists of securing and setting up a Linux server to host Item Catalog Web application project.

  - Server IP Address: 157.230.166.104
  - URL: http://www.157.230.166.104.xip.io
  - SSH Port: 2200
  - SSH Username: grader

# 1. Setting up server
  - Create an account on [DigitalOcean Server](https://cloud.digitalocean.com/login).
  - Click Create Droplet.
  - Choose Ubuntu 16.04.5 x64.
  - I choose a $5 base-level droplet.
  - Choose a datacenter region.
  - In this level, I did not set up the SSH key.
  - Click Create.

#### Step1: Root Login
- To login as root user, you can use the following command: 
```sh
$ ssh root@157.230.166.104
```
- I accepted the warning about host authenticity. Then I provided my root password sent to my email address by DigitalOcean. (I changed the password after login).
#### Step2: Update and upgrade packages
- To Update and upgrade packages use the following command: 
```sh
$ sudo apt update && apt upgrade
```
- you might need to reboot the server by running the following command: 
```sh
$ sudo reboot
```
#### Step3: Set time zone to UTC
- Run the following command: 
```sh
$ sudo dpkg-reconfigure tzdata
```
- Choose "None of these," then choose UTC in the list and press enter. 
#### Step4: Create a New User: Garder
- While loging in as root run the following command to create a new user namde `grader`. I have entered password `grader` for the user grader:
```sh
$ adduser grader
```
- The terminal will generate the following output:
```sh
  Adding user `grader' ... 
  Adding new group `grader' (1000) ...
  Adding new user `grader' (1000) with group `grader' ...
  Creating home directory `/home/grader' ...
  Copying files from `/etc/skel' ...
  Enter new UNIX password:
  Retype new UNIX password:
  passwd: password updated successfully
  Changing the user information for grader
  Enter the new value, or press ENTER for the default
	  Full Name []: grader
	  Room Number []:
	  Work Phone []:
	  Home Phone []:
	  Other []:
  Is the information correct? [Y/n]
```
#### Step5: Add Garder to the sudo group
- `grader` user must be able to run commands with administrative privileges. To do so use the following command to add grader user to the sudo group:
```sh
$ gpasswd -a grader sudo
```
- You can run commands with sudo to verify.
#### Step6: Add Public Key Authentication
- On your local machine, open a new terminal window and generate SSh key by typing the following command:
```sh
$ ssh-keygen
```
- The terminal will display this output:
```sh
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/localUser/.ssh/id_rsa):
```
- Press enter to accept the given file. After that, the terminal will ask you to enter a passphrase. In my case leave it blank so I can use the private key for authentication without entering a passphrase.
```sh
Created directory '/Users/localUser/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
- The terminal will prompt the generation of the private and public key saved in `.ssh/id_rsa.pub`.
- Next, type this command to copy to print the generated public key:
```sh
cat ~/.ssh/id_rsa.pub
```
- The terminal will display your public SSH Key. Select the public SSH and copy it.
- On the server, connect as grader user and create a new directory called .ssh with restrict permissions:
```sh
$ mkdir .ssh
$ chmod 700 .ssh
```
- Open a file in .ssh named `authorized_keys` with nano command so we can edit it:
```sh
$ nano .ssh/authorized_keys
```
- Paste your public key. Hit CTRL+X then confirm changes and press Enter.
- As security step we must restrict the permissions of the authorized_keys file:
```sh
$ chmod 600 .ssh/authorized_keys
$ exit
```
#### Step7: Change SSH port to 2200
- Login as root user and open the `/etc/ssh/sshd_config`file using nano to edit it:
```sh
nano /etc/ssh/sshd_config
```
- Find the line `Port 22` and change it to `Port 2200. Hit CTRL+X then confirm changes and press Enter.
- Restart the SSH server:
```sh
service ssh restart
```
- To test the changes type : `exit`
- Now connect on port 2200 using this command :
```sh
ssh root@157.230.166.104 -p 2200
```
#### Step8: Secure server
- In this level, we must configure the firewall to allow only these connections: `SSH (port 2200)`, `HTTP (port 80)` and `NTP (port 123)`. Run this command:
```sh
ufw allow 2200/tcp
ufw allow 80/tcp
ufw allow 123/udp
ufw enable
```
- To validate these changes: 
```sh
ufw status
```
- You must get the following prompt: 
```sh
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)
```
#### Step9: Disable Root Login
- To disable remote SSH access to the root account open the `/etc/ssh/sshd_config`file using nano to edit it
```sh
nano /etc/ssh/sshd_config
```
- change PermitRootLogin from yes to `no` and PasswordAuthentication from yes to `no`. Hit CTRL+X then confirm changes and press Enter.
- Restart SSH:
```sh
service ssh restart
```
- Exit and login with grader:
```sh
exit
ssh grader@157.230.166.104 -p 2200
```
# 2. Install Apache
- To install apache server use these commands:
```sh
sudo apt update
sudo apt install apache2
```
In your browser, type http://157.230.166.104. The Apache Default page will appear. 
Dillinger is currently extended with the following plugins. Instructions on how to use them in your own application are linked below.
# 3.  PostgreSQL
- As first step install PostgreSQL using these commands:
```sh
sudo apt-get install libpq5
sudo apt-get install postgresql
sudo su - postgres
```
- Access to the SQL interpreter with `psql`and type these SQL commands:
```sh
$ psql
#CREATE USER catalog WITH PASSWORD 'grader';
#ALTER USER catalog CREATEDB;
#CREATE DATABASE catalog WITH OWNER catalog;
#\c catalog
#You are now connected to database "catalog" as user "postgres".
#catalog=REVOKE ALL ON SCHEMA public FROM public;
#REVOKE
#catalog=GRANT ALL ON SCHEMA public TO catalog;
GRANT
```
- To exit interactive Postgres type `\q`.
# 3.  Install required Packages
- First, install `python-pip`:
```sh
$ sudo apt install python-pip
$ pip --version
```
- Then, install python packages:
```sh
$ sudo pip install --upgrad Flask httplib2 oauth2client sqlalchemy psycopg2 psycopg2-binary  requests
```
- To clone Item Catalog application we need Git.
Git should already be pre-installed, we have to configure it:
```sh
git config --global user.name "Your NAME"
git config --global user.email "myemail@domain.com"
```
- Now your git is ready to clone the Item Catalog App. We will clone our app in the default root folder of apache server (/var/www/) and we will make grader the owned of the catalog directory:
```sh
sudo git clone git:https://github.com/NeilaCH/Udacity-Item-Catalog.git /var/www/catalog
sudo chown -R grader:grader /var/www/catalog
```
-  Apache server requires `mod_wsgi` to run python. To do so we will install wsgi :
```sh
$ sudo apt install libapache2-mod-wsgi
```
This command will automatically activate wsgi. So we only need to restart our apache server:
```sh
$ sudo service apache2 restart
```
If you are using puthon 2.7 the main application name must be `__init__.py` so the `mod_wsgi` can recognize it. To do so, in var/www/catalog rename `main.py` using this command: 
```sh
mv main.py __init__.py
```
#### Configure client secret and _ _init__.py
- Step1: Client Secret. Connect to your project in [Google API credentials](https://console.cloud.google.com/apis/credentials) and add the IP address as Authorized redirect URIs and Authorized JavaScript origins. 
### Please Note: 
Google API will not accept public IPs. You need to have a domain name. In my case, I used [wip.io](http://xip.io). Once you got your domain name do not forget to add it in your droplet.
- Step2: Update the client secret using this command:
```sh
cd /var/www/catalog
sudo touch client_secrets.json
sudo nano client_secrets.json
```
- Step3: Update the _ _init__.py file by modifying:
  - CLIENT_ID = json.loads(open('/var/www/catalog/client_secrets.json', 'r')
                      .read())['web']['client_id']
  - engine = create_engine('postgresql://catalog:grader@localhost/catalog')
  - if _ _name__ == "_ _main__":
    app.secret_key = '-??ȞGqJ??b=S??'
    app.debug = True
    app.run()
- Step4: Update the database_setup.py and list_itmcateg.py ny modifying:
   - engine = create_engine('postgresql://catalog:grader@localhost/catalog')
# 4. Set up Configuration File
- Set up `catalog.conf` file using nano to configure the virtual host:
```sh
$ sudo nano /etc/apache2/sites-available/catalog.conf
```
- Type the following:
```sh
<VirtualHost *:80>
   ServerName 157.230.166.104
   ServerAlias www.157.230.166.104.xip.io
   ServerAdmin chettaoui.neila@gmail.com
    WSGIDaemonProcess application user=grader threads=3
   WSGIScriptAlias / /var/www/catalog/catalog.wsgi
   <Directory /var/www/catalog/>
    WSGIProcessGroup application
    WSGIApplicationGroup %{GLOBAL}
    Require all granted
  </Directory>
   Alias /static /var/www/catalog/static
   <Directory /var/www/catalog/static/>
       Order allow,deny
       Allow from all
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/error.log
   LogLevel warn
   CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
# 5. Create Wsgi File
- In this step we will create a wsgi file : 
```sh
$ cd /var/www/catalog/
$ sudo nano catalog.wsgi
```
- Add the following lines:
```sh
# - *- coding: utf-8 - *-
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")
from __init__ import app as application
application.secret_key = '-??HGqJ??b=S??'
```
- enable virtual host and restart apache server:
 ```sh
sudo a2ensite catalog
sudo service apache2 restart
```
#### Debug
If you are getting errors, you can check out Apache's error log for debugging:
 ```sh
 sudo tail /var/log/apache2/error.log
```

## Resourses:
- Set Up Sudo and SSH Keys [DigitalOcean Tutorial](https://www.digitalocean.com/community/tutorials/video-how-to-set-up-sudo-and-ssh-keys-on-ubuntu-14-04).
- Public IP error on google APIS [Stackoverflow](https://stackoverflow.com/questions/14238665/can-a-public-ip-address-be-used-as-google-oauth-redirect-uri).
- Install and Use PostgreSQL [DigitalOcean Tutorial](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04).
- Python on DigitalOcean [DigitalOcean Tutorial](https://www.digitalocean.com/community/tags/python?type=tutorials).
- Flask application and wsgi [DigitalOcean Tutorial](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uswgi-and-nginx-on-ubuntu-18-04).
- Add Domain Name [DigitalOcean Tutorial](https://www.digitalocean.com/docs/networking/dns/how-to/add-domains/).
- View Appache Error [Stackoverflow](https://stackoverflow.com/questions/16613340/view-apache-error-log-in-real-time).
