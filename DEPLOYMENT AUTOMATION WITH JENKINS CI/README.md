# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION USING JENKINS

In previous Project 8, we introduced the horizontal scalability concept, which allows us to add new Web Servers to our Tooling Website. You have successfully deployed a set-up with 2 Web Servers and a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task repeatedly adding dozens or even hundreds of servers.

DevOps is about Agility and the speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is the Automation of routine tasks.
In this project, we are going to start automating part of our routine tasks with a free and open-source automation server – [Jenkins](https://en.wikipedia.org/wiki/Jenkins_(software)).

Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in [GitHub](https://github.com/IwunzeGE/DevopsToolingWebsite) will be automatically be updated to the Tooling Website.

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
1.	Enable webhooks in your GitHub repository settings. **NOTE: end the payload url with /github-webhook/**
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/webhook1.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/webhook2.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/4ad6cf4f7fcf8e72eecd002fe35df1ad7fca7c5a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/webhook3.png)

2.	Go to Jenkins web console, click "New Item" and create a "Freestyle project"
![a](new job.png](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/new%20job.png)
**NOTE: YOU MIGHT HIT THIS ISSUE**
![A](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/blocker1.png)

**FIX**
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/fix1.png)

In the configuration of your Jenkins freestyle project choose Git repository, and provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/psswd%20and%20usrname%20git.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/repo%20url.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/credenitals%20add.png)

Save the configuration and let us try to run the build. For now, we can only do it manually.
Click the "Build Now" button, if you have configured everything correctly, the build will be successful and you will see it under #1

![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/build.png)

You can open the build and check in "Console Output" if it has run successfully.
If so – congratulations! You have just made your very first Jenkins build!
But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/console%20output.png)

3.	Click "Configure" your job/project and add these two configurations. Configure triggering the job from the GitHub webhook:
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/build%20triggers.png)

Configure "Post-build Actions" to archive all the files – files resulting from a build are called "artifacts".
![a](https://github.com/IwunzeGE/DevOps-Project/blob/b4437c432b9fb8041c3df75554e87ed6dd28452a/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/archive.png)

Now, go ahead and make some changes in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.
You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on the Jenkins server

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/auto%20build.png)

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and file transfer is initiated by GitHub). There are also other methods: trigger one job (downstream) from another (upstream), poll GitHub periodically and others.
By default, the artifacts are stored on the Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/local%20%20build%20store.png)

## Step 3 – Configure Jenkins to copy files to NFS server via SSH
Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.
Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

1.	Install the "Publish Over SSH" plugin.
On the main dashboard select "Manage Jenkins" and choose the "Manage Plugins" menu item.
On the "Available" tab search for the "Publish Over SSH" plugin and install it

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/publish%20over%20ssh.png)

2.	Configure the job/project to copy artifacts over to the NFS server.
On the main dashboard select "Manage Jenkins" and choose the "Configure System" menu item.
Scroll down to Publish over the SSH plugin configuration section and configure it to be able to connect to your NFS server:
1.	Provide a private key (the content of .pem file that you use to connect to the NFS server via SSH/Putty)
2.	Arbitrary name
3.	Hostname – can be private IP address of your NFS server
4.	Username – ec2-user (since the NFS server is based on EC2 with RHEL 8)
5.	Remote directory – /mnt/apps since our Web Servers use it as a mounting point to retrieve files from the NFS server
Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/nfs%20config%201.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/nfs%20config%202.png)

Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/nfs%20config%203.png)

Save this configuration and go ahead, and change something in README.MD file in your GitHub Tooling repository.
Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:
```SSH: Transferred 25 file(s)
Finished: SUCCESS
```

**NOTE: YOU COULD ENCOUNTER AN ISSUE OF UNSTABLE BUILD**
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/blocker2.png)

**FIX**
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/fix2.png)
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/fix22.png)

To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check README.MD file
`cat /mnt/apps/README.md`. I used the grep command to filter the exact text with which I updated the README.md file

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ec9bc6a45d644bbd5a3c8f911b113dbb647ac523/DEPLOYMENT%20AUTOMATION%20WITH%20JENKINS%20CI/images/cat%20mnt.png)

If you see the changes you had previously made in your GitHub – the job works as expected.
Congratulations!
You have just implemented your first Continous Integration solution using Jenkins CI. Watch out for advanced CI configurations in upcoming projects.
