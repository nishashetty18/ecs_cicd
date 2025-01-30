FROM jenkins/jenkins:lts

USER root

# Install AWS CLI
RUN apt-get update && apt-get install -y awscli

# Install Docker CLI
RUN apt-get install -y docker.io

# Ensure Jenkins user can run Docker
RUN usermod -aG docker jenkins

USER jenkins
