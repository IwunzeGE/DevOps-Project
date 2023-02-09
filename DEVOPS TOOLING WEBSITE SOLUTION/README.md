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

