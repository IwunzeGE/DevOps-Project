# DEVOPS TOOLING WEBSITE SOLUTION

## Side Self Study
Read about Network-attached storage (NAS), Storage Area Network (SAN) and related protocols like NFS, (s)FTP, SMB, and iSCSI. Explore what Block-level storage is and how it is used by Cloud Service providers, and know the difference from Object storage.
On the example of AWS services understand the difference between Block Storage, Object Storage and Network File System.

## Setup and technologies used
As a member of a DevOps team, you will implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.
In this project you will implement a solution that consists of following components:
1.	Infrastructure: AWS
2.	Webserver Linux: Three Red Hat Enterprise Linux 8
3.	Database Server: One Ubuntu 20.04 + MySQL
4.	Storage Server: One Red Hat Enterprise Linux 8 + NFS Server
5.	Programming Language: PHP
6.	Code Repository: GitHub
On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

![A](https://github.com/IwunzeGE/DevOps-Project/blob/eedcfb28cf50ef2e9f8dcf93f4cca07c318f2900/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/server%20diagram.png)

It is important to know what storage solution is suitable for what use cases, for this – you need to answer the following questions: what data will be stored, in what format, how this data will be accessed, by whom, from where, how frequently, etc. Based on this you will be able to choose the right storage system for your solution.
 

## STEP 1 – PREPARE NFS SERVER
1.	Spin up a new EC2 instance with RHEL Linux 8 Operating System.
-	Based on your LVM experience from the [three-tier architecture project](https://github.com/IwunzeGE/DevOps-Project/blob/123636af62cf9854ec71bc0b21e1687b1952d162/THREE-TIER%20ARCHITECTURE/README.md), Configure LVM on the Server.

-	Instead of formatting the disks as ext4, you will have to format them as xfs
![a](https://github.com/IwunzeGE/DevOps-Project/blob/123636af62cf9854ec71bc0b21e1687b1952d162/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/format%20xfs.png)

-	Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs
![a](https://github.com/IwunzeGE/DevOps-Project/blob/123636af62cf9854ec71bc0b21e1687b1952d162/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/lvcreate%20apps,logs,opt.png)

3.	Create mount points on /mnt directory for the logical volumes as follow:
```Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in the next project
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/d02ae2a89b6729382b23527a3e219f08137e90ec/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/mkdir.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/d02ae2a89b6729382b23527a3e219f08137e90ec/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/mount.png)

4.	Install NFS server, configure it to start on reboot and make sure it is up and running
```sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```
5.	Export the mounts for webservers’ subnet CIDR to connect as clients. For simplicity, you will install all three Web Servers inside the same subnet, but in the production set-up, you would probably want to separate each tier inside its own subnet for a higher level of security.

To check your subnet cidr – open your EC2 details in the AWS web console and locate the ‘Networking’ tab and open a Subnet link:
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b5c049f898bad3b63444ac15fcb8a21a5509e357/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/ipv4%20cidr1.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b5c049f898bad3b63444ac15fcb8a21a5509e357/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/ipv4%20cidr2.png)


Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:
```sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt
 
sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt
 ```
`sudo systemctl restart nfs-server.service`

Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):

`sudo nano /etc/exports`
 
```/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b5c049f898bad3b63444ac15fcb8a21a5509e357/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/etc-exports.png)

sudo exportfs -arv

![a](https://github.com/IwunzeGE/DevOps-Project/blob/b5c049f898bad3b63444ac15fcb8a21a5509e357/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/export-arv.png)

6.	Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)
`rpcinfo -p | grep nfs`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/b5c049f898bad3b63444ac15fcb8a21a5509e357/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/rpcinfo.png)

**Important note:** In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049.

![a](https://github.com/IwunzeGE/DevOps-Project/blob/b5c049f898bad3b63444ac15fcb8a21a5509e357/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/inbound%20rules.png)

## STEP 2 — CONFIGURE THE DATABASE SERVER
By now you should know how to install and configure a MySQL DBMS to work with remote Web Server
1.	Install MySQL server
2.	Create a database and name it tooling
3.	Create a database user and name it webaccess
4.	Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr

![a](https://github.com/IwunzeGE/DevOps-Project/blob/b5c049f898bad3b63444ac15fcb8a21a5509e357/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/database.png)

## STEP 3 — PREPARE THE WEB SERVERS

We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL database.
You already know that one DB can be accessed for reads and writes by multiple clients. For storing shared files that our Web Servers will use – we will utilize NFS and mount previously created Logical Volume lv-apps to the folder where Apache stores files to be served to the users (/var/www).
This approach will make our Web Servers stateless, which means we will be able to add new ones or remove them whenever we need, and the integrity of the data (in the database and on NFS) will be preserved.
During the next steps we will do following:
•	Configure NFS client (this step must be done on all three servers)
•	Deploy a Tooling application to our Web Servers into a shared NFS folder
•	Configure the Web Servers to work with a single MySQL database
1.	Launch a new EC2 instance with RHEL 8 Operating System
2.	Install NFS client
`sudo yum install nfs-utils nfs4-acl-tools -y`
3.	Mount /var/www/ and target the NFS server’s export for apps
`sudo mkdir /var/www`
`sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www`
4.	Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:
![a](https://github.com/IwunzeGE/DevOps-Project/blob/3cb3602530a8e23fbda5801d0470507b496447b3/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/df%20apps.png)

`sudo vi /etc/fstab`
add following line
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
1[a](https://github.com/IwunzeGE/DevOps-Project/blob/3cb3602530a8e23fbda5801d0470507b496447b3/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/fstab.png)
 
5.	Install Remi’s repository, Apache and PHP
 
 ```sudo yum install httpd -y
 
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y
 
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y 
sudo dnf module reset php
 
sudo dnf module enable php:remi-7.4 -y
 
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd -y
 	
sudo systemctl start php-fpm
 
sudo systemctl enable php-fpm
 
sudo setsebool -P httpd_execmem 1
```
**Repeat steps 1-5 for another 2 Web Servers.**

6.	Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files – it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other Web Servers.

7.	Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. Repeat step №4 to make sure the mount point will persist after reboot.

![a](https://github.com/IwunzeGE/DevOps-Project/blob/3cb3602530a8e23fbda5801d0470507b496447b3/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/df%20logs.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/3cb3602530a8e23fbda5801d0470507b496447b3/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/fstab%20logs.png)

8.	Fork the tooling source code from [this repo](https://github.com/IwunzeGE/DevopsToolingWebsite) to your Github account. (Learn how to fork a repo [here](https://youtu.be/f5grYMXbAV0))
9.	Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

![a](https://github.com/IwunzeGE/DevOps-Project/blob/e0e209d1d8cd9ebacadfaa803e56383f07819c3a/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/images/cp%20html.png)
