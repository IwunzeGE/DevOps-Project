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

- Create our Jenkinsfile

Inside the Ansible project, create a new directory deploy and start a new file Jenkinsfile inside the directory.

![jenkinsfile created](https://user-images.githubusercontent.com/110903886/224506029-a4999ad9-9b60-409a-b444-0f3af67d742e.png)

Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage

```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```

- Now go back into the Ansible pipeline in Jenkins, and select configure

- Scroll down to Build Configuration section and specify the location of the Jenkinsfile at `deploy/Jenkinsfile`

![pipepline cofig0](https://user-images.githubusercontent.com/110903886/224506267-e54eb851-315d-4531-9bd9-0b15d39e1810.png)

![pipepline cofig1](https://user-images.githubusercontent.com/110903886/224506263-81965df5-bb4b-4155-99fb-757bac51a1dd.png)

![pipepline cofig2](https://user-images.githubusercontent.com/110903886/224506265-28ed181c-d8a8-42bb-a359-56cc41b09ac7.png)

![pipeline build](https://user-images.githubusercontent.com/110903886/224507239-e949b681-2c33-4e85-ad1d-993a87c2a80e.png)

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.

![pipepline cofig3](https://user-images.githubusercontent.com/110903886/224506476-86c1a6d1-4c24-4ece-b692-5af9789cfa7c.png)

![pipepline cofig4](https://user-images.githubusercontent.com/110903886/224506486-b1550b2b-aa2c-4e29-98fe-f5bc5acffe22.png)

View this with Blue ocean

![blueocean0](https://user-images.githubusercontent.com/110903886/224506878-be180270-59cc-4bea-821d-f6a7c960cf6c.png)

![blueocean1](https://user-images.githubusercontent.com/110903886/224506885-81f9f752-e42a-499c-bee1-08f03db14daa.png)

Let us see this in action.

1.	Create a new git branch and name it feature/jenkinspipeline-stages
2.	Currently we only have the Build stage. Let us add another stage called Test. Paste the code snippet below and push the new changes to GitHub.

```
   pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
```

3.	To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.

![scan repo now](https://user-images.githubusercontent.com/110903886/224506753-0ccff958-31dc-48d2-93a4-380d6edeadd5.png)

4.	In Blue Ocean, you can now see how the Jenkinsfile has caused a new step in the pipeline launch build for the new branch.

![blueocean2](https://user-images.githubusercontent.com/110903886/224506838-659bda30-cdae-495b-92e0-4eabe819904f.png)

5. Add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build and test stages)
   - Package 
   - Deploy 
   - Clean up
   
6. Verify in Blue Ocean that all the stages are working, then merge your feature branch to the main branch

![added more stages to branch](https://user-images.githubusercontent.com/110903886/224507096-2a2f010f-73d0-446a-84a8-975aa2086a7a.png)

7. Eventually, your main branch should have a successful pipeline like this in blue ocean.

![added more stages to main](https://user-images.githubusercontent.com/110903886/224507124-853c8bef-ad2e-41b2-aa39-9fce57b65c94.png)


## RUNNING ANSIBLE PLAYBOOK FROM JENKINS

Now that you have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work by:
1.	Installing Ansible on Jenkins Server

```
sudo yum install ansible -y
python3 -m pip install --upgrade setuptools
python3 -m pip install --upgrade pip
python3 -m pip install PyMySQL
python3 -m pip install mysql-connector-python
python3 -m pip install psycopg2-binary
```

2.	Installing Ansible plugin in Jenkins UI

![install ansible plugin](https://user-images.githubusercontent.com/110903886/224507402-230170d8-2ed0-48c0-a7f9-aa65523500ce.png)

3. Add credentials in Jenkins UI

![jenkins manage credentials0](https://user-images.githubusercontent.com/110903886/224507475-f52cf1e4-87df-4ac7-b723-db66e1da9b55.png)

![jenkins manage credentials1](https://user-images.githubusercontent.com/110903886/224507503-4c0937fd-fdcb-431e-9c08-3a8c935a8fd8.png)

![jenkins manage credentials2](https://user-images.githubusercontent.com/110903886/224507509-05b96bee-d7f1-4fdc-83bb-9dd103a58206.png)

![jenkins manage credentials3](https://user-images.githubusercontent.com/110903886/224507518-207ac6eb-bcc6-4340-b288-58139ec109f4.png)








5.	Creating Jenkinsfile from scratch. (Delete all you currently have in there and start all over to get Ansible to run successfully)




ansible.cfg

```
[defaults]
timeout = 160
callback_whitelist = profile_tasks
log_path=~/ansible.log
host_key_checking = False
gathering = smart
ansible_python_interpreter=/usr/bin/python3
allow_world_readable_tmpfiles=true


[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=30m -o ControlPath=/tmp/ansible-ssh-%h-%p-%r -o ServerAliveInterval=60 -o ServerAliveCountMax=60 -o ForwardAgent=yes
```





