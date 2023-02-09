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

On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files
