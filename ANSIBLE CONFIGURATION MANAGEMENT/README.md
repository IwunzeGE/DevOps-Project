# ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE 


## Ansible Client as a Jump Server (Bastion Host)

A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface.
On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

![server 00](https://user-images.githubusercontent.com/110903886/220224181-be4dc5f9-344a-4606-92b2-a52b47e5aca4.png)

## Task
- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration

## INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE
1.	Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
2.	In your GitHub account create a new repository and name it ansible-config-mgt.
3.	Install Ansible 
`sudo apt update` `sudo apt install ansible`

Check your Ansible version by running `ansible --version`

![ansive version](https://user-images.githubusercontent.com/110903886/220224433-2f332370-1b1b-41a8-8975-f8f0341c2a6b.png)

4.	Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.
- Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
- Configure Webhook in GitHub and set webhook to trigger ansible build.
- Configure a Post-build job to save all (**) files, like you did it in Project 9.

![jenkins job 1](https://user-images.githubusercontent.com/110903886/220224642-83965ebf-abc5-4dbc-885c-6d0632e17962.png)

![jenkins job 3](https://user-images.githubusercontent.com/110903886/220224666-4a1212a6-573f-45af-927d-8902214fffcc.png)

5.	Test your setup by making some change in README.MD file in main branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

`ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

**Note: Trigger Jenkins project execution only for /main (master) branch.**

Now your setup will look like this:

![server 0](https://user-images.githubusercontent.com/110903886/220224757-1e0a1467-bf27-4c28-803a-e6e94a45311b.png)

## Step 2 – Prepare your development environment using Visual Studio Code

6.	First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC)

7.	After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

8.	Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

9.	git clone <ansible-config-mgt repo link>

## BEGIN ANSIBLE DEVELOPMENT
10.	In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.

*Tip: Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool – include ticket number (e.g. PRJ-145) in the name of your branch and add a topic and a brief description what this branch is about – a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)*

![ansible branch](https://user-images.githubusercontent.com/110903886/220225406-86f7adbe-cfef-4c47-9f8d-8fa48171f2b5.png)

11.	Checkout the newly created feature branch to your local machine and start building your code and directory structure
12.	Create a directory and name it playbooks – it will be used to store all your playbook files.
13.	Create a directory and name it inventory – it will be used to keep your hosts organised.
14.	Within the playbooks folder, create your first playbook, and name it common.yml
15.	Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.
  
![ansible folders](https://user-images.githubusercontent.com/110903886/220225324-12781162-5455-4eb3-bbcd-22c3af948637.png)

## Step 4 – Set up an Ansible Inventory
An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.
  
Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

**Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:**

**To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:
•	For Windows users – [ssh-agent on windows](https://youtu.be/OplGrY74qog)
•	For Linux users – [ssh-agent on linux](https://youtu.be/OplGrY74qog)**

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

Confirm the key has been added with the command below, you should see the name of your key
ssh-add -l

Now, ssh into your Jenkins-Ansible server using ssh-agent
ssh -A ubuntu@public-ip

**Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.**
  
Update your inventory/dev.yml file with this snippet of code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```

![ssh](https://user-images.githubusercontent.com/110903886/220225624-7f183cbf-3e0e-4f7c-b7a7-cb5db59d88ed.png)

