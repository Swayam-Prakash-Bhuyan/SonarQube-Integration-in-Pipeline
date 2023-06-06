# Docker Installation and SonarQube Setup Guide

This guide provides step-by-step instructions for installing Docker, setting up a SonarQube server using Docker, and configuring SonarQube analysis in Jenkins. SonarQube is a popular open-source platform for static code analysis that helps detect code quality issues and vulnerabilities in your software projects.

## Prequicite:
Refer this to install some of the plugins and Jenkins declarative pipeline code
https://github.com/Swayam-Prakash-Bhuyan/Jenkins-Pipeline-Deployment-On-Tomcat.git
just exclude tomcat


## Step 1: Docker Installation

Open a terminal.
Run the following commands:

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch=\"$(dpkg --print-architecture)\" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo apt install docker-compose
Restart the Docker service:


sudo service docker restart
Add your user to the docker group to run Docker commands without sudo:

sudo usermod -aG docker $USER
newgrp docker
Adjust permissions for the Docker socket:


sudo chmod 666 /var/run/docker.sock
sudo systemctl restart docker

## Step 2: SonarQube Server Setup

Run the following command to start the SonarQube server:

docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
Wait for the container to start. You can check the status using the docker ps command.

Access the SonarQube web interface by opening your browser and entering http://<your-ip-address>:9000.

Use the default credentials to log in: Username = admin, Password = admin.

After logging in, change the password to a secure one.


## Step 3: Custom Quality Profile Setup

Inside SonarQube, navigate to "Administration" > "Quality Profiles".

Click on "Create" to create a new profile.

Select the relevant programming language (Java or TypeScript) and customize the rules according to your requirements.

Save the profile.


## Step 4: Jenkins Plugin Installation

Go to your Jenkins instance.

Navigate to "Manage Jenkins" > "Manage Plugins" > "Available" tab.

Search and select the following plugins to install: Eclipse Temurin Installer, OpenJDK Native Plugin, OWASP Dependency Check, Docker, Docker Pipeline, Docker Build Step, and CloudBees Docker Build and Publish.

Click "Install without restart" to install the selected plugins.


## Step 5: SonarQube Configuration in Jenkins

In Jenkins, go to "Manage Jenkins" > "Configure System".

Scroll down to the "SonarQube servers" section.

Click on "Add SonarQube" to configure a new SonarQube server.

Provide the server name and SonarQube URL (http://<your-ip-address>:9000).

Generate a token in SonarQube: Go to "Administration" > "Security" > "Users" > "Token" and create a new token.

Copy the generated token.

In Jenkins, click on "Add" next to "Server Authentication Token" and enter the copied token.

Save the configuration.

    
## Step 6: SonarQube Analysis in Jenkins Pipeline

Assuming you already have a Jenkins pipeline, add the following stage after the "Compile" stage:

groovy
stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('sonar-server') {
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Onlinebook \
                -Dsonar.java.binaries=. \
                -Dsonar.projectKey=Onlinebook'''
        }
    }
}
This stage runs the SonarQube scanner with the specified project settings. Adjust the values according to your project.

Make sure you have the sonar-scanner tool configured in Jenkins Global Tool Configuration.

Save and run your Jenkins pipeline to trigger SonarQube analysis.

Remember to adjust the configurations and pipeline code to match your specific setup and requirements.


That's it! You have now successfully installed Docker, set up a SonarQube server, and configured SonarQube analysis in Jenkins. Follow the instructions provided to adapt the steps to your specific project and environment. Happy coding and analyzing your code quality!
