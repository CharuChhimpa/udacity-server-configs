## Configs
### Creating Digital Ocean Server
* From Create button choose droplet option (Create Droplets Form will open)
* Choose 'Ubuntu 16.04' distribution
* Choose $5/mo plan
* Choose preferred data center location
* Create new ssh key named 'do_root'
    * run ```ssh-keygen``` on your local machine terminal
    * save your key to path ```/Users/USER_NAME/.ssh/do_root```
* Copy  ```/Users/USER_NAME/.ssh/do_root.pub``` content to clipboard
* Return to create droplets form and click on new ssh button
* Paste the clipboard data to modal form and name it 'do_root'
* Choose the preferred no. of droplet
* Name your droplet
* CLick on 'Create' button

### Logging into Your server
Copy your droplet's IP address
and run below command to login to your server
```ssh  -i ~/.ssh/do_root root@YOUR_DROPLET_IP```
### Updating The installed packages
```
sudo apt-get update
sudo apt-get upgrade
```
Other Updates
```
sudo apt-get install unattended-upgrades
sudo unattended-upgrades
```

### Changing ssh port from 22 to 2200
Open ssh config file and change port from 22 to 2200
```
sudo nano /etc/ssh/sshd_config
```

Restart ssh service
```
sudo service ssh restart
```
 
 Now you need to specify pot no. to login
```ssh -p 2200  -i ~/.ssh/do_root root@YOUR_DROPLET_IP```

### Config UFW
Check current status of UFW
```
sudo ufw status
```

ConfigureUFW to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

```
sudo ufw allow ssh
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw allow ntp
```
Enable UFW
```
sudo ufw enable
```

### Create new user
run below command to create new user named grader
```sudo adduser grader```

Go through the procedure and provide the necessary details like password, name etc

### Give grader the permission to sudo.
First Create ```sudoers.d``` file
```touch /etc/sudoers.d/grader```
Next, Add permission
```
sudo nano /etc/sudoers.d/grader
# Opens grader file
```
Now add ``` grader ALL=(ALL) NOPASSWD:ALL``` to file and save

### Create an SSH key pair for grader using the ssh-keygen tool
* run ```ssh-keygen``` on your local machine terminal
* save your key to path ```/Users/USER_NAME/.ssh/do_grader```
* Copy  ```/Users/USER_NAME/.ssh/do_grader.pub``` content to clipboard
* Now switch user on server terminal by ```su - grader```
* create directory named ```.ssh`` by ```mkdir .ssh```
* create file ```authorized_keys``` by ```nano .ssh/authorized_keys```
* give correct permission to file by ```chmod 644 .ssh/authorized_keys```
* give correct permission to `.ssh` folder by `chmod 700 .ssh`
* restart service by `sudo service ssh restart`
Now you can login as `grader` by `ssh -p 2200  -i ~/.ssh/do_grader grader@YOUR_DROPLET_IP`

### Configure the local timezone to UTC.
In ubuntu 16.04 you can directly set timezone simply through below command
`sudo timedatectl set-timezone Etc/UTC`

### Install and configure Apache to serve a Python mod_wsgi application.
First install Apache server by
```
sudo apt-get install apache2
```

Verify if apache is running fine by visiting
`http://YOUR_DROPLET_IP`

Install wsgi_mod
`sudo apt-get install libapache2-mod-wsgi.`

Restart Apache server

### Install and configure PostgreSQL

Install PostgreSQl
`sudo apt-get install postgresql`

To Disable remote connection open `sudo nano /etc/postgresql/VERSION/main/pg_hba.conf` and reove entry other than `127.0.0.1`

To Create new database and user we need to enter into psql shell which can be achieved by `sudo -u postgres psql`

After entering the shell enter below command
Create new user with `catalog` database access by command `create user catalog with password 'password';`
Create database by command `create database catalog with owner catalog;`
Then exit by `\q`

### Install git
run below command to install git
`sudo apt-get install git` 

### Clone and setup your Item Catalog project from the Github

get to `www` the directory by `cd /var/www`
clone by `sudo git clone https://github.com/ansarimofid/udacity-catalog.git catalog`
get to `catalog` directory `cd catalog`
install `pip` by `sudo apt-get install python-pip`
install dependencies by `sudo -H pip install -r requirements.txt`

### Configure Apache to run the catalog app
Create apache config file
`sudo nano /etc/apache2/sites-available/catalog.conf`

Configure it by copying below config text
```
<VirtualHost *:80>
    ServerName YOUR_IP

    WSGIScriptAlias / /var/www/catalog/wsgi.py

    <Directory /var/www/catalog>
        Order allow,deny
        Allow from all
    </Directory>
</VirtualHost>
```

go to project directory by `cd /var/www/catalog`
Create  `wsgi` file by `sudo nano wsgi.py`
Add following config
```python
import sys

sys.path.insert(0, '/var/www/catalog')

from catalog import app as application

application.secret_key = 'New secret key. Change it on server'
```

Enable and reload apache server
`sudo a2ensite catalog`
`sudo service apache2 reload`

Check for errors by `sudo cat /var/log/apache2/error.log`

### Setting Up subdomain to run your app
Since google oauth doesn't work with bare IPs we need to setup subdomain
You can use `https://www.cloudflare.com` and link your IP to subdomain

### Changing redirect link
Open catalog.py file by `sudo nano /var/www/catalog/catalog.py` and replace the redirect link by `YOUR_SUBDOMAIN`
### Changing configs in google OAuth 2.0
configure your OAuth 2.0 credential and update `uthorized origin` and `redirect URIs` with your subdomain

### Disable root login and password based login
Open config file `sudo nano /etc/ssh/sshd_config`
Change the following
`
PermitRootLogin no
PasswordAuthentication no
`
