# EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP

Ansible Inventory should look like this

```
├── ci
├── dev
├── pentest
├── pre-prod
├── prod
├── sit
└── uat
```


- ci inventory file

```
[jenkins]
<Jenkins-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[sonarqube]
<SonarQube-Private-IP-Address>

[artifact_repository]
<Artifact_repository-Private-IP-Address>
```

- dev Inventory file

```
[tooling]
<Tooling-Web-Server-Private-IP-Address>

[todo]
<Todo-Web-Server-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<DB-Server-Private-IP-Address>
```

- pentest inventory file

```
[pentest:children]
pentest-todo
pentest-tooling

[pentest-todo]
<Pentest-for-Todo-Private-IP-Address>

[pentest-tooling]
<Pentest-for-Tooling-Private-IP-Address>
```

ANSIBLE ROLES FOR CI ENVIRONMENT

Now go ahead and Add two more roles to ansible:

1.	SonarQube (Scroll down to the Sonarqube section to see instructions on how to set up and configure SonarQube manually)
2.	Artifactory

## Configuring the Jenkins Server
- Install jenkins with its dependencies

```
sudo wget -O /etc/yum.repos.d/jenkins.repo \    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum upgrade
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

# Add required dependencies for the jenkins package

sudo yum install java-11-openjdk -y
sudo yum install jenkins -y
sudo systemctl daemon-reload
```

- Update the bash profile

`sudo -i`

`nano .bash_profile`

```
export JAVA_HOME=$(dirname $(dirname $(readlink $(readlink $(which java)))))
export PATH=$PATH:$JAVA_HOME/bin 
export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
```

- Reload the bash profile

`source ~/.bash_profile`

**NB: This is done so that the path is exported anytime the machine is restarted**

- Start the jenkins server

```			
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```

## Configuring Ansible For Jenkins Deployment

In previous projects, you have been launching Ansible commands manually from a CLI. Now, with Jenkins, we will start running Ansible from Jenkins UI.
To do this,

1.	Navigate to Jenkins URL
2.	Install & Open Blue Ocean Jenkins Plugin
3.	Create a new pipeline
4.	Select GitHub
5.	Login to GitHub & Generate an Access Token

![github access token](https://user-images.githubusercontent.com/110903886/224505736-5dc3df10-e6ae-42cd-b534-ad01c41f7e40.png)

![github access token1](https://user-images.githubusercontent.com/110903886/224505740-a9561f49-e904-4ce4-b757-206f4e4df078.png)

7.	Copy Access Token
8.	Paste the token and connect
9.	Create a new pipelne

![create pipeline](https://user-images.githubusercontent.com/110903886/224505620-f173cef5-0591-4507-bd37-d48c855cd220.png)

![create pipeline1](https://user-images.githubusercontent.com/110903886/224505633-23cd7379-ba09-455d-ab9c-71a76df49fc8.png)

At this point you may not have a Jenkinsfile in the Ansible repository, so Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on Administration to exit the Blue Ocean console.


![administration](https://user-images.githubusercontent.com/110903886/224505887-c20d8a49-7d6d-48ff-bc67-0cabfa1fe7ab.png)


