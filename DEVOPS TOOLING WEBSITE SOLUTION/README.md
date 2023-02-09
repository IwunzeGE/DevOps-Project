# DEVOPS TOOLING WEBSITE SOLUTION

## Side Self Study

Read about Network-attached storage (NAS), Storage Area Network (SAN) and related protocols like NFS, (s)FTP, SMB, and iSCSI. Explore what Block-level storage is and how it is used by Cloud Service providers, and know the difference from Object storage.

On the example of AWS services understand the difference between Block Storage, Object Storage and Network File System.


## Setup and technologies

As a member of a DevOps team, you will implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.

In this project you will implement a solution that consists of following components:

 - Infrastructure: AWS

- Webserver Linux: Three Red Hat Enterprise Linux 8

- Database Server: One Ubuntu 20.04 + MySQL

- Storage Server: One Red Hat Enterprise Linux 8 + NFS Server

- Programming Language: PHP

- Code Repository: GitHub

On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

## STEP 1 – PREPARE NFS SERVER

### Step 1 – Prepare NFS Server

- Spin up a new EC2 instance with RHEL Linux 8 Operating System.

- Based on your LVM experience from Project 6, Configure LVM on the Server.

- Instead of formatting the disks as ext4, you will have to format them as xfs

- Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs

- Create mount points on /mnt directory for the logical volumes as follow:

Mount lv-apps on /mnt/apps – To be used by webservers

Mount lv-logs on /mnt/logs – To be used by webserver logs

Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

- Install NFS server, configure it to start on reboot and make sure it is u and running

** Note: It is important to know what storage solution is suitable for what use cases, for this – you need to answer the following questions: what data will be stored, in what format, how this data will be accessed, by whom, from where, how frequently, etc. Based on this you will be able to choose the right storage system for your solution.**

```
sudo yum -y update

sudo yum install nfs-utils -y

sudo systemctl start nfs-server.service

sudo systemctl enable nfs-server.service

sudo systemctl status nfs-server.service


``
```
``
`
`

`

`

