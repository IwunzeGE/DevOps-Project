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

Create a Security Group for:


