# WEB SOLUTION WITH WORDPRESS

This repository explains the steps involved in preparing storage infrastructure on two Linux servers and implementing a basic web solution using WordPress.

You will gain practical experience that demonstrates three-tier architecture while also making sure that the Linux servers' storage disks are properly partitioned and maintained using tools like gdisk and LVM, respectively.

## The 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/three-tier.png)

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

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/ls%20dev.png)

4.	Use df -h command to see all mounts and free space on your server

![al](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/df%20-h.png)

5.	Use fdisk utility to create a single partition on each of the 3 disks
`sudo fdisk /dev/xvdf`

![al](https://github.com/IwunzeGE/DevOps-Project/blob/a9acc7f3c0df813d98b46f050670461879a42b6a/THREE-TIER%20ARCHITECTURE/images/sudo%20gdisk2.png)

6.	Use `lsblk` utility to view the newly configured partition on each of the 3 disks.

![al](https://github.com/IwunzeGE/DevOps-Project/blob/be5073e286dc1b1ea1c4a12117e92a2f567c9a1a/THREE-TIER%20ARCHITECTURE/images/lsblk%20part.png)

7. Install lvm2 package using `sudo yum install lvm2`. Run sudo lvmdiskscan command to check for available partitions.

*Note: yum is the package installer for RedHat/CentOS*

8. Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/pvcreate.png)

9.	Verify that your Physical volume has been created successfully by running `sudo pvs`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/pvs.png)

10.	Use `vgcreate` utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg
 
`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`
 
11.	Verify that your VG has been created successfully by running `sudo vgs`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/vgcreate%20&%20vgs.png)

12. Use `lvcreate` utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. 
**NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.**
 
```sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

13.	Verify that your Logical Volume has been created successfully by running `sudo lvs`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/lvcreate.png)

14.	Verify the entire setup 
`sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/vgdisplay.png)

`sudo lsblk`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/sudo%20lsblk.png)

15. Use mkfs.ext4 to format the logical volumes with ext4 filesystem
 
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
 
![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/mkfs.png)

16.	Create /var/www/html directory to store website files
 
`sudo mkdir -p /var/www/html`
 
17.	Create /home/recovery/logs to store backup of log data
 
`sudo mkdir -p /home/recovery/logs`

18.	Mount /var/www/html on apps-lv logical volume
 
`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`
 
19.	Use rsync utility to back up all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
 
`sudo rsync -av /var/log/. /home/recovery/logs/`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/mkdir,%20mount,%20resync.png)

20.	Mount /var/log on logs-lv logical volume. 
*(Note that all the existing data on /var/log will be deleted. That is why step 15 above is very important)*

`sudo mount /dev/webdata-vg/logs-lv /var/log`
 
21.	Restore log files back into /var/log directory
 
`sudo rsync -av /home/recovery/logs/. /var/log`
 
![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/mount%20&%20resync.png)

21.	Update /etc/fstab file so that the mount configuration will persist after restart of the server. 
- The UUID of the device will be used to update the /etc/fstab file;
`sudo blkid`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/blkid.png)

- `sudo nano /etc/fstab`
*Note: you have to install nano text editor first using `sudo yum install nano`
![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/nano%20etc-fstab.png)

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/nano%20etc-fstab%202.png)

22. Test the configuration and reload the daemon 
```sudo mount -a
sudo systemctl daemon-reload
```

23.	Verify your setup by running `df -h`, output must look like this:

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/df%20-h%202.png)

#STEP 2 - PREPARE THE DATABASE SERVER

Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv and logs-lv, create db-lv and logs-lv, then mount it to /db directory instead of /var/www/html/.

#STEP 3 - INSTALL WORDPRESS ON YOUR WEB SERVER
1.	Update the repository

`sudo yum -y update`
 
2.	Install wget, Apache and it’s dependencies
 
`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/install%20wget%20httpd.png)

3.	Start Apache
```sudo systemctl enable httpd
sudo systemctl start httpd
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/enable%20httpd.png)

4.	To install PHP and its depemdencies
 
```sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

5.	Restart Apache
`sudo systemctl restart httpd`
 
6.	Download wordpress and copy wordpress to var/www/html

```mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
sudo cp -R wordpress /var/www/html/
```

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/mkdir%20wordpress%20sudo%20wget.png)

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/cp%20wordpress.png)

7.	Configure SELinux Policies
``
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
sudo setsebool -P httpd_can_network_connect=1
sudo setsebool -P httpd_can_network_connect_db 1
```

![a](https://github.com/IwunzeGE/DevOps-Project/blob/10d8b69bedf06f1feda38efff29df59f9ba92bf4/THREE-TIER%20ARCHITECTURE/images/chown.png)
