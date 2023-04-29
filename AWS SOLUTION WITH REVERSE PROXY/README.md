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
- Create a domain name for your company. I used [namecheap](www.namecheap.com).
- Create a hosted zone in AWS, and map it to your domain.
![Alt text](images/route1.png)
![Alt text](images/route2.png)
![Alt text](images/route3.png)
![Alt text](images/route4.png)
![Alt text](images/route5.png)