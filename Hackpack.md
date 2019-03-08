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
* Check what's in the crontab
	* crontab -l
* Edit the crontab
	* crontab -e
* Check /tmp for suspicious files
	* ls -la /tmp
* Check people with sudo permission. Do not let users have ALL = (ALL) ALL.
	* visudo
* Check running processes
	* ps aux
	* top
* Check running services
	* ss -tlpn
* Set up firewall
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
#### Commands
#### Reset MySQL Root Password
#### Create backup of MySQL Database
### Apache
### Nginx
### OpenSSH
### VSFTPD
### ProFTPd
TODO: fill in all these

## List of Common Commands
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

### firewalld
