# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

## General Overview

You will build a secure infrastructure inside AWS VPC (Virtual Private Cloud) network for a fictitious company named WakaBetter. that uses WordPress CMS for its main business website, and a [Tooling Website](https://github.com/IwunzeGE/tooling) for their DevOps team. As part of the company’s desire for improved security and performance, a decision has been made to use a reverse proxy technology from NGINX to achieve this.
Cost, Security, and Scalability are the major requirements for this project. Hence, implementing the architecture designed below, ensure that infrastructure for both websites, WordPress and Tooling, is resilient to Web Server’s failures, can accomodate to increased traffic and, at the same time, has reasonable cost.

![architecture](images/architecture.png)

## Requirements

### There are few requirements that must be met before you begin:

- Properly configure your AWS account and Organization Unit Watch How To Do This Here
    - Create an AWS Master account. (Also known as Root Account)
    - Within the Root account, create a sub-account and name it DevOps. (You will need another email address to complete this)
    ![Alt text](images/organization1.png)
    ![Alt text](images/organization2.png)
    ![Alt text](images/organization3.png)
    ![Alt text](images/organization4.png)
    - Within the Root account, create an AWS Organization Unit (OU). Name it Dev. (We will launch Dev resources in there).
   ![Alt text](images/organization5.png)
   ![Alt text](images/organization6.png)
    - Move the DevOps account into the Dev OU.
   ![Alt text](images/organization7.png)
   ![Alt text](images/organization8.png)
   ![Alt text](images/organization9.png)
    - Login to the newly created AWS account using the new email address.
    ![Alt text](images/organizationsss.png)
    **Note:** If you can't find your password, Just do a password reset from the console.
- Create a domain name for your company. I used [namecheap](www.namecheap.com).
- Create a hosted zone in AWS, and map it to your domain.
![Alt text](images/route1.png)
![Alt text](images/route2.png)
![Alt text](images/route3.png)
![Alt text](images/route4.png)
![Alt text](images/route5.png)

## SET UP A VIRTUAL PRIVATE NETWORK (VPC)

1. Create a VPC
![Alt text](images/vpc1.png)
![Alt text](images/vpc2.png)
![Alt text](images/vpc3.png)
2. Enable DNS hosting
![Alt text](images/vpc4.png)
![Alt text](images/vpc5.png)
3. Create internet gateway as shown in the architecture
![Alt text](images/ig1.png)
![Alt text](images/ig2.png)
![Alt text](images/ig3.png)
![Alt text](images/ig4.png)
![Alt text](images/ig5.png)

4. Create the subnets

Use this website to get the CIDR blocks easily. [ipinfo](https://ipinfo.io/ips).

![Alt text](images/ip1.png)
![Alt text](images/ip2.png)
![Alt text](images/ip3.png)

![Alt text](images/sn1.png)
![Alt text](images/sn2.png)
![Alt text](images/sn3.png)
![Alt text](images/sn4.png)
![Alt text](images/sn5.png)
![Alt text](images/sn6.png)

5. Create a route tables and associate it with private and public subnets.
![Alt text](images/route11.png)
![Alt text](images/route22.png)
![Alt text](images/route33.png)
![Alt text](images/route44.png)

6. Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accessisble from the Internet).
![Alt text](images/routess.png)
![Alt text](images/routess22.png)

7. Create 3 Elastic IPs
![Alt text](images/elastic.png)
![Alt text](images/elastic2.png)

8. Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by Bastion hosts)
![Alt text](images/nat1.png)
![Alt text](images/nat2.png)
![Alt text](images/nat3.png)
![Alt text](images/nat4.png)

9. Create a Security Group for:
![Alt text](images/sg1.png)

- Application Load Balancer: ALB will be available from the Internet.

![Alt text](images/sg2.png)
![Alt text](images/sg2.1.png)

- Bastion Servers: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address.
![Alt text](images/sg3.png)

- Nginx Servers: Access to Nginx should only be allowed from a Application Load balancer (ALB).
![Alt text](images/sg4.png)
![Alt text](images/sg4.1.png)

- Internal Application Load Balancer: ALB will be available from the Nginx servers.
![Alt text](images/sg5.png)

- Webservers: Access to Webservers should only be allowed from the Internal Application Load Balancer.
![Alt text](images/sg6.png)

- Data Layer: Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.

![Alt text](images/sg7.png)


## TLS Certificates From Amazon Certificate Manager (ACM)
You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB).

- Navigate to AWS ACM

![Alt text](images/acm1.png)

- Request a public wildcard certificate for the domain name you registered.

![Alt text](images/acm2.png)
![Alt text](images/acm3.png)
![Alt text](images/acm4.png)

- Use DNS to validate the domain name
![Alt text](images/acm5.png)
![Alt text](images/acm6.1.png)
![Alt text](images/acm6.2.png)
![Alt text](images/acm7.png)
![Alt text](images/acm8.png)
![Alt text](images/acm9.png)


## Configure EFS
- Create a new EFS File system. Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer. Associate the Security groups created earlier for data layer.

![Alt text](images/efs1.png)
![Alt text](images/efs2.png)
![Alt text](images/efs3.png)
![Alt text](images/efs4.png)
![Alt text](images/efs5.png)
![Alt text](images/efs6.png)
![Alt text](images/efs7.png)

- Create 2 access points - ! for each of the website (wordpress and tooling) so that the files do not overwrite each other when we mount.
![Alt text](images/efs8.png)
![Alt text](images/efs9.png)
![Alt text](images/efs10.png)
![Alt text](images/efs11.png)
![Alt text](images/efs12.png)
![Alt text](images/efs13.png)
![Alt text](images/efs14.png)

## Configure RDS
### Pre-requisite: 
- Create a KMS Key
![Alt text](images/kms1.png)
![Alt text](images/kms2.png)
![Alt text](images/kms3.png)
![Alt text](images/kms4.png)
![Alt text](images/kms5.png)
![Alt text](images/kms6.png)

To ensure that yout databases are highly available and also have failover support in case one availability zone fails, we will configure a multi-AZ set up of RDS MySQL database instance. In our case, since we are only using 2 AZs, we can only failover to one, but the same concept applies to 3 Availability Zones.

To configure RDS, follow steps below:
1. Create a subnet group and add 2 private subnets (data Layer)
![Alt text](images/rds1.png)
![Alt text](images/rds2.png)
![Alt text](images/rds3.png)
![Alt text](images/rds4.png)

2. Create the DB
![Alt text](images/rds5.png)
![Alt text](images/rds6.png)
![Alt text](images/rds7.png)
![Alt text](images/rds8.png)
![Alt text](images/rds9.png)


### Configure Loadbalancers and Target Groups

1. Create Target group for NGINX, tooling amd wordpress targets

![Alt text](images/target1.png)
![Alt text](images/target2.png)
![Alt text](images/target3.png)
![Alt text](images/target4.png)
![Alt text](images/target5.png)

NB: I didn't add any instance to the targets because I havent laucnhed them. I'll do that kater in and add them to the target group.

1. Create public-facing and internal loadbalancers

![Alt text](images/lb1.png)
![Alt text](images/lb2.png)
![Alt text](images/lb3.png)
![Alt text](images/lb4.png)
![Alt text](images/lb5.png)
![Alt text](images/lb6.png)

Repeat the same procedure for internal ALB, select wordpress as the default target and create a rule to send traffic to tooling if the headers match our specified parameters.

![Alt text](images/lb7.png)
![Alt text](images/lb8.png)
![Alt text](images/lb9.png)
![Alt text](images/lb10.png)
![Alt text](images/lb11.png)
![Alt text](images/lb12.png)
![Alt text](images/lb13.png)