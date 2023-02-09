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
Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in the next project
4.	Install NFS server, configure it to start on reboot and make sure it is up and running
```sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```
