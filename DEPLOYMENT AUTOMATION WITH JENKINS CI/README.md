# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION USING JENKINS

In previous Project 8, we introduced the horizontal scalability concept, which allows us to add new Web Servers to our Tooling Website. You have successfully deployed a set-up with 2 Web Servers and a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task repeatedly adding dozens or even hundreds of servers.

DevOps is about Agility and the speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is the Automation of routine tasks.
In this project, we are going to start automating part of our routine tasks with a free and open-source automation server – [Jenkins](https://en.wikipedia.org/wiki/Jenkins_(software)).

Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/<yourname>/tooling will be automatically be updated to the Tooling Website.

## Side Self Study
Read about [Continuous Integration, Continuous Delivery and Continuous Deployment](https://circleci.com/continuous-integration/)

## Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, and configuring a job to automatically deploy source code changes from Git to the NFS server.
Here is what your updated architecture will look like upon completion of this project
![a](https://github.com/IwunzeGE/DevOps-Project/blob/3968a8921d2312ea8fc5bbb22b38b0043b9f0212/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/server%200.png)

## INSTALL AND CONFIGURE JENKINS SERVER

1.	Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"
2.	Install JDK (since Jenkins is a Java-based application)
`sudo apt update`
`sudo apt install default-jdk-headless`
3.	Install Jenkins

```wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list
sudo apt update
sudo apt-get install jenkins
```
    
Make sure Jenkins is up and running
`sudo systemctl status jenkins`
    ![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/systemctl%20status.png)

4.	By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group
 ![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/custom%20tcp%208080.png)

5.	Perform initial Jenkins setup.
- From your browser access `http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080`
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/jenkins%20login%20page.png)
- You will be prompted to provide a default admin password

Retrieve it from your server:
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/sudo%20cat%20admin%20password.png)
    
- Then you will be asked which plugings to install – choose suggested plugins.
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/custtomize%20jenkins.png)

- Once plugin installation is done – create an admin user and you will get your Jenkins server address.
The installation is completed!
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/create%20admin%20user.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/jenkins%20ready.png)
 
### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.
1.	Enable webhooks in your GitHub repository settings
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/webhook1.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/webhook2.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/webhook3.png)

