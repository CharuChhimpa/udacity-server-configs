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

