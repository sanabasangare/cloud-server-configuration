# Linux Server Installation

### About:
This documentation outlines the steps to a baseline installation of a Linux distribution on a virtual machine for hosting applications. This process includes performing basic packages update as well as securing the server.

### Basic Configuration
Server Info:

    - IP Address: 35.164.28.52
    - SSH Port: 2200
    - URL: http://ec2-35-164-28-52.us-west-2.compute.amazonaws.com/

Installation:
- Download and extract your private key file (if any).
- Move the file into your development environment directory and start the server.

1- Log into the virtual machine as root
```sh
$ ssh -i ~/.ssh/key.rsa root@ip
```

2- Create a user with sudo permissions
```sh
$ adduser grader
```
Grant this user sudo permissions by creating a new file
```sh
$ nano /etc/sudoers.d/grader
```
Add the following line, then save the file
```sh
grader ALL=(ALL:ALL) ALL
```
Solve the "sudo: unable to resolve host" error by editing the hosts file
```sh
$ nano /etc/hosts
```
Add your host and ip # and save
```sh
$ 127.0.0.1 localhost ip 10.xx.xx.xx
```

3- Update all installed packages
```sh
$ apt-get update
$ apt-get upgrade
```

4- Configure the local timezone to UTC
```sh
$ dpkg-reconfigure tzdata
Pick "None of the above" and then "UTC"
```
Use "date" to check
```sh
$ date
```

### Securing the Server
5- Edit the SSH port from 22 to 2200
```sh
$ nano /etc/ssh/sshd_config
Change the port
$ 22  to 2200
```
Also add the following line at the bottom of the page before saving the "sshd_config" file.
```sh
AllowUsers grader
```

-[As root] Set-up SSH keys for user (grader)
```sh
$ mkdir /home/user/.ssh
$ chown user:user /home/user/.ssh
$ chmod 700 /home/user/.ssh
$ cp /root/.ssh/authorized_keys /home/user/.ssh/
$ chown grader:grader /home/user/.ssh/authorized_keys
$ chmod 644 /home/user/.ssh/authorized_keys
```

6- Configure the Uncomplicated Firewall (UFW)

SSH into the server as the new "user" with port 2200
```sh
ssh -i ~/.ssh/key.rsa user@ip -p 2200
```

Set the UFW to only allow for SSH, HTTP, and NTP
```sh
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
$ sudo ufw allow 2200/tcp
$ sudo ufw allow www
$ sudo ufw allow ntp
$ sudo ufw enable
```

To check the status of your rules and make sure that the firewall is active, use the command:
```sh
$ sudo ufw status
```
[or]
```sh
$ sudo ufw status verbose
```

### Deploying the application

7-Install and configure Apache to serve a Python mod_wsgi application
```sh
$ sudo apt-get install apache2
$ sudo apt-get install libapache2-mod-wsgi
$ sudo a2enmod wsgi
$ sudo service apache2 restart
```
Verify if Apache is running with:
```sh
$ sudo service apache2 status
```
Then, by visiting [http://xx.xxx.xx.xx/] (public ip address) or [http://ec2-xx-xxx-xx-xx.us-west-2.compute.amazonaws.com/]

To configure Apache to handle requests using the WSGI module, edit:
```sh
$ sudo nano /etc/apache2/sites-enabled/000-default.conf
```

Then, restart Apache with:
```sh
$ sudo apache2ctl restart
```

8-Install and configure PostgreSQL
```sh
$ sudo apt-get install postgresql postgresql-contrib
```
Change to the default PostgresSQL profile "postgres"
```sh
$ sudo su - postgres
# psql
# CREATE USER catalog WITH PASSWORD 'catalog'
# CREATE DATABASE catalog WITH OWNER catalog
# \q
```

Installing other `packages` & `modules`
```sh
$ su user (grader) to log back in as user (grader)

$ sudo apt-get install python-pip
$ sudo pip install flask
$ sudo pip install sqlalchemy
$ sudo apt-get install sqlite3 libsqlite3-dev
$ sudo pip install oauth2client
$ sudo pip install requests
$ sudo pip install httplib2
```

9-Install git, clone and set up your application directory
```sh
$ sudo apt-get install git
$ cd /var/www
$ sudo mkdir musicology
$ cd musicology
$ sudo git clone https://github.com/user/catalog.git musicology
$ sudo python database_setup.py
$ sudo python subgenres.py
$ sudo python __init__.py
```

Configure and Enable the New Virtual Host
```sh
$ sudo nano /etc/apache2/sites-available/myapp.conf
$ sudo a2dissite 000-default.conf
$ sudo a2ensite myapp.conf
```
Create a wsgi file corresponding to the WSGIScriptAlias in the ".conf" document
```sh
$ sudo nano /var/www/app.wsgi
```

Write the logic of the application and save it.
```sh
$ sudo service apache2 reload
```

10-The Amazon EC2 Instance's public URL:
```sh
http://ec2-35-164-28-52.us-west-2.compute.amazonaws.com/
```

*TIP: for debugging, it's crucial to read and understand the error log during testing*

For example, use:
```
$ sudo tail -20 /var/log/apache2/error.log
```
or (on Unix systems)
```
$ tail -f error_log
```

Localhost:
```sh
127.0.0.1
```

### Sources
- [Udacity - Configuring Linux Web Servers Course](https://udacity.com/)
- [StackExchange - SuperUser](http://superuser.com/)
- [StackExchange - Unix & Linux](http://unix.stackexchange.com/)
- [Ubuntu Forums](https://ubuntuforums.org/index.php)
- [Digital Ocean - Community](https://www.digitalocean.com/community/)

License
----
MIT Copyright (c) 2016 Sanaba Sangare
