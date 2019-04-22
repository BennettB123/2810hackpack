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
**Testing**
*ftp [IP] or ftp localhost

*Source: https://phoenixnap.com/kb/how-to-setup-ftp-server-install-vsftpd-centos-7*

### ProFTPd
TODO: fill in all these

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
	* Update Kernel 
	* Systemtap
		* Save File under [filename].stg
		``probe kernel.function("mem_write").call ? { $count = 0 }``
		``probe syscall.ptrace {  // includes compat ptrace as well $request = 0xfff}``
		``probe begin { printk(0, "CVE-2016-5195 mitigation loaded")}``
		``probe end {printk(0, "CVE-2016-5195 mitigation unloaded")}``

* Resource: https://bugzilla.redhat.com/show_bug.cgi?id=1384344#c13*
		* sudo yum install stap-prep
		* sudo yum install systemtap systemtap-runtime
		* stap -g [filename-from-step-1].stp
		* If system reboots, this needs to be done again.
	
	* ksplice
		* Patches kernel in memory. No reboot
		* Doesnt support CentOS use Kpatch
			* Ksplice by Oracle (for Oracle Linux updates, Ksplice Uptrack for enterprise)
			* Kpatch by Red Hat (for RHEL kernel updates and CentOS updates)
			* Livepatch by Canonical (for Ubuntu kernel updates)
			* Kgraft by SUSE (for SUSE updates only)
			* CloudLinux KernelCare (for multiple OSes)
	* Kpatch (unsure of current patch needed)
		* yum install kpatch
		* yum install kpatch-patch-7.0-1.el7.x86_64.rpm
		* yum update kpatch-patch-7.0-2.el7.x86_64.rpm
		* kpatch unload kpatch_7_0_2_el7
		* kpatch uninstall kpatch_7_0_2_el7
		* kpatch uninstall --kernel-version 3.10.0-121.el7.x86_64 kpatch_7_0_2_el7
## Limiting User Resources 
* ulimit
	* List current setting - *ulimit -a*
	* Set Soft limit - *ulimit -S [value]*
	* Set Hard limit  - *ulimit -H [value]*

## Updating Kernel (for Ubuntu)
* sudo apt-get update
* sudo apt-get dist-upgrade

## Updating Kernel (for centOS)
* Update and upgrade packages
	* yum -y update
* Makes kernel update faster
	* yum -y install yum-plugin-fastestmirror
* Check OS Version
	* cat /etc/redhat-release
	* cat /etc/os-release
**Continue only if Centos 7**
* Check kernel version
	* uname -msr
* Add ELRepo repository
	* Add repo key - rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
	* Add repo - rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
	* Check ELRepo is on list - yum repolist
* Install new kernel
	* yum --enablerepo=elrepo-kernel install kernel-ml
* Configure Grub
	* View kernels on machine - sudo awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
	* Set kernel to latest version - sudo grub2-set-default 0
	* sudo grub2-mkconfig -o /boot/grub2/grub.cfg
	* sudo reboot
* Check if kernel has been updated
	* uname -msr
*Optional*
*Remove other kernels (if 3 or more installed)
	* yum install yum-utils
	* package-cleanup --oldkernels
**Centos 6 and below**
* Update packages
	* sudo -y upgrade
* Check kernel version
	* uname -msr
* Upgrade kernel
	* yum update kernel*
* Set kernel to most recent
	* vim /boot/grub/grub.conf
	* Set default=0
* Check kernel was updated
	* uname -msr

*Resources:* 
	* *https://www.howtoforge.com/tutorial/how-to-upgrade-kernel-in-centos-7-server/*
	* **

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
	
