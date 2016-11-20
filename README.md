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

1-Log into the virtual machine as root
```sh
$ ssh -i ~/.ssh/key.rsa root@ip
```

2-Create a user with sudo permissions
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
Use:
```sh
$ date [to check]
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

.....
