# CPSC 2810 Team 4 Hackpack

Keep track of everything you change so that you and others are up to date on the state of your machine and so it can be used for future reference.

## Linux 30-Minute Checklist
* Change the root password.
	* passwd root
* Check /etc/passwd to see users. Everyone should have bin/false or bin/nologin.
	* cat /etc/passwd
* Change passwords of other users with login.
	* passwd username
* Check /etc/group and /etc/shadow.
	* cat /etc/group and cat /etc/shadow
* Check what's in the crontab and edit it.
	* crontab -l
	* crontab -e
* Check /tmp for suspicious files.
	* ls -la /tmp
* Check people with sudo permission. Do not let users have ALL = (ALL) ALL.
	* visudo
* Check running processes.
	* ps aux
	* top
* Check running services.
	* ss -tulpn
* Update and upgrade
	* yum -y update
* Set up firewall.
	* See the firewall section
	* TODO: find out how to link to other parts of a markdown file, if that's possible. then link this part to the firewall section.

## Windows 30-Minute Checklist
* GFY
* "Go through every single option on AD" - Max
* TODO: make actual checklist

## Disaster Recovery
TODO: List disaster recovery steps

## Secure Service Configuration
### MySQL
#### Installation
* sudo mysql_secure_installation
#### Commands
#### Reset MySQL Root Password
* sudo /etc/init.d/mysql stop
* sudo /usr/sbin/mysqld --skip-grant-tables --skip-networking &
* mysql -u root
* FLUSH PRIVLEGES
* SET PASSWORD FOR root@'localhost' = PASSWORD('password');
* UPDATE mysql.user SET Password=PASSWORD('newpwd') WHERE User='root';

#### Create backup of MySQL Database
15 2 * * * root mysqldump -u root -pPASSWORD --all-databases | gzip > /mnt/disk2/database_'data ' %m-%d-%y' ' .sql.gz
### Apache2
* Configuration file is found in /etc/apache2/apache2.conf
* Restart, start, or stop apache2
	* /etc/init.d/apache2 [restart | stop | start]
### Nginx
* /etc/nginx/nginx.conf
* Audit the server
	* sudo apt-get install wapiti
	* wapiti http://example.org -n 10 -b
folder
### OpenSSH
* etc/ssh/sshd_config
* Deny root login
	* etc/ssh/sshd_config
	* PermitRootLogin no
* Limit user login
	* /etc/ssh/sshd_config
	* AllowUsers (username)
* Disable Protocol 1
	* Protocol 2
* User public/private keys for auth
	* put pubkey in ~/.ssh/authorized_keys
	* PasswordAuthentication no
### VSFTPD
**Installing Vsftpd**
1. Install vsftpd - sudo yum install vsftpd 
2.Start service - sudo systemctl start vsftpd
3. Enable start on launch - sudo systemctl enable vsftpd
4. Create firewall rule
	* sudo firewall-cmd --zone=public --permanent --add-port=21/tcp
	* sudo firewall-cmd --zone=public --permanent --add-service=ftp
	* sudo firewall-cmd -reload

**Editing Config files**
1. Make copy of config files
	*sudo cp /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.default
2. Disable anon users, allow local users, allow users to upload files
	* anonymous_enaable=NO
	* local_enable=YES
	* write_enable=YES
3. Limit ftp users to directory.
	* userlist_enable=YES
	* userlist_file=/etc/vsftpd/user_list
	* userlist_deny=NO (Setting this to yes makes user_list a list of blocked users)
4. Sudo systemctl restart vsftpd

**Adding User**
1. Create ftp user
	* sudo adduser testuser
	* sudo passwd testuser
2. Add user to userlist
	* echo "testuser" | sudo tee -a /etc/vsftpd/user_list
3. Create direcories and setting up permissions
	* sudo mkdir –p /home/testuser/ftp/upload
	* sudo chmod 550 /home/testuser/ftp
	* sudo chmod 750 /home/testuser/ftp/upload
	* sudo chown –R testuser: /home/testuser/ftp

*Source: https://phoenixnap.com/kb/how-to-setup-ftp-server-install-vsftpd-centos-7*

### ProFTPd
TODO: fill in all these

## List of Common Commands & Flags
* sudo (superuser do)
* ls (list)
	* -a: all, shows hidden files, files with a . in front of it
	* -l: long, shows more info such as file owner, group, and permissions
* cd (change directory)
	* cd /home
* ps aux (process status)
	* -a: show running processes for all users
	* -u: show processes for specified usernames
	* -x: includes processes that do not have a controlling terminal
	* -e: everything, all processes
	* -f: format
* top (list processes)
* ss (socket statistics)
	* -t: display TCP sockets
	* -u: display UDP sockets
	* -l: display only listening sockets
	* -p: show process using the socket
	* -n: do not try to resolve service name
* crontab
	* -l: displays current crontab
	* -e: edit current crontab
* ip
	* TODO: talk about ip
* cat (concatenate, outputs file contents to terminal)
* grep (finds and prints similar text in directory)
	* grep "text you want found"
	* -r: recursive
* file (identifies file type)
	* file helloworld.txt
* nmap (scans for open ports)
* chmod (change mode of file)
	* chmod 400 helloworld.txt
	* TODO: add what each of the octal numbers mean
* chgrp (change group of file)
* chown (change file owner)
* passwd (change password)
	* passwd Barney
* visudo (edit the sudoers file)
	* sudo visudo
* rm (remove)
	* rm -rf /tmp
	* -r (recursive, for directories)
	* -f (force, won’t ask for confirmation)
* deluser (delete user)
	* deluser Barney

## Firewall
### UFW
* Install UFW
	* apt install ufw
* Enable/start UFW
	* ufw enable
* Check status
	* ufw status
* Allow/deny a service
	* ufw allow http
	* ufw deny ftp
* Allow/deny a port
 	* ufw allow 80
	* ufw deny 21

### firewalld
* Install firewalld
	* yum install firewalld
* Enable firewalld
	* systemctl enable firewalld
* Start firewalld
	* systemctl start firewalld
* Check status
	* firewall-cmd state
* Allow/deny a service
	* firewall-cmd --add-service=http --permanent
	* firewall-cmd --remove-service=ftp --permanent
* Allow/deny a port
	* firewall-cmd --add-port=80/tcp --permanent
	* firewall-cmd --remove-port=21/tcp --permanent
* Reload firewalld
	* firewall-cmd reload
	
## Dirty Cow
* To protect against it
	* update kernel 
	* systemtap 
	* ksplice
		* patches kernel in memory. No reboot
## Limiting User Resources
* ulimit
	* List current setting - **ulimit -a**
	* Set Soft limit - **ulimit -S [value]**
	* Set Hard limit  - **ulimit -H [value]**
	
