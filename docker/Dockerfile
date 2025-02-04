FROM jenkins/jenkins:lts

USER root

RUN apt-get update && apt-get install sudo -y  
 
# Install AWS CLI
RUN apt-get update && apt-get install -y awscli

# Install Docker CLI
RUN apt-get install -y docker.io

RUN systemctl Start docker

RUN systemctl Start jenkins

# Ensure Jenkins user can run Docker
RUN usermod -aG docker jenkins




USER jenkins
