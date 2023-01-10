# WEB SOLUTION WITH WORDPRESS

This repository explains the steps involved in preparing storage infrastructure on two Linux servers and implementing a basic web solution using WordPress.

You will gain practical experience that demonstrates three-tier architecture while also making sure that the Linux servers' storage disks are properly partitioned and maintained using tools like gdisk and LVM, respectively.

## The 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

![alt]()

Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.
Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

1.	Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
2.	Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
3.	Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server

# STEP 1 - PREPARE A WEB-SERVER
1.	Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.
![alt](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/volumes.png)

![al](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/available%20volumes.png)

2.	Attach all three volumes one by one to your Web Server EC2 instance

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/attach%20volumes.png)

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/attach%20volumes%202.png)

3. Open up the Linux terminal to begin configuration Use `lsblk` command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with `ls /dev/` and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

![al](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/lsblk.png)

4.	Use df -h command to see all mounts and free space on your server

![al](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/df%20-h.png)

5.	Use fdisk utility to create a single partition on each of the 3 disks
`sudo fdisk /dev/xvdf`

![al](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/sudo%20gdisk2.png)

6.	Use lsblk utility to view the newly configured partition on each of the 3 disks.

![al](https://github.com/IwunzeGE/DevOps-Project/blob/be5073e286dc1b1ea1c4a12117e92a2f567c9a1a/THREE-TIER%20ARCHITECTURE/images/lsblk%20part.png)

7. Install lvm2 package using `sudo yum install lvm2`. Run sudo lvmdiskscan command to check for available partitions.

*Note: yum is the package installer for RedHat/CentOS*
